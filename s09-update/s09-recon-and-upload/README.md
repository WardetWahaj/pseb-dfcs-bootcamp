# S09 — Recon & Web App Pentesting

**Author:** Raja Wardet Wahaj (Wolf)
**Program:** DFCS Bootcamp, Atomcamp (Cohort 2)
**Environment:** Kali Linux (attacker) / an authorised target domain + DVWA lab, isolated network

Full write-up: `day5-homework.docx` (add it to this folder).

---

## Task 1 — Scope Document and Rules of Engagement

A hypothetical engagement against `*.intel.com`. Full scope, break-glass clause, and rules of engagement are documented separately so it can be reused or cited on its own:

[**View the Scope Document and RoE**](scope/intel-scope-and-roe.md)

---

## Task 2 — Google Dork Report

Six dorks run against `microsoft.com`, a domain used here only to demonstrate public-indexing recon technique, not an authorised target of an actual engagement. Everything found is already public; nothing was exploited.

| # | Dork | What it targets | Result (observed) | Fix |
|---|---|---|---|---|
| 1 | `site:microsoft.com filetype:pdf "confidential"` | Documents marked confidential, indexed | Several PDFs returned, all legitimate published whitepapers (e.g. "Confidential Computing", "Toward Confidential Cloud Computing") — no sensitive internal documents leaked | Remove from web root; set `noindex`; restrict access |
| 2 | `site:microsoft.com ext:env "DB_PASSWORD"` | Exposed environment files with credentials | No `.env` files found; results were Microsoft Learn documentation pages that merely *mention* the string `DB_PASSWORD` as a variable name in code samples | Delete `.env` from web root; rotate credentials if ever exposed |
| 3 | `site:microsoft.com inurl:admin -inurl:login` | Admin interfaces without a login path | Only legitimate public pages returned (Microsoft 365 Admin Center docs, Exchange Admin Center announcement, Power Pages admin docs) — no exposed admin panel | Require authentication; IP-restrict admin |
| 4 | `site:microsoft.com intitle:"index of"` | Open directory listings | A few genuine open directory listings on `packages.microsoft.com` (e.g. `/docs/`, `/centos/7/prod/Packages/`) — package repositories, intentionally public | Disable directory indexing (`Options -Indexes`) where not intended |
| 5 | `site:microsoft.com intext:"phpinfo()"` | phpinfo pages disclosing config | **Zero results** — Google returned "did not match any documents" | N/A here; still: delete `phpinfo.php` from any web root if present |
| 6 | `site:microsoft.com filetype:sql OR filetype:bak` | Exposed database dumps / backup files | No `.sql`/`.bak` files matched; results were unrelated Microsoft Marketplace SQL-product listing pages | Remove backups; block `.sql`/`.bak` from being served |

![Dork 1 — confidential PDFs](dorks/f2-dork-01.png)
![Dork 2 — DB_PASSWORD env files](dorks/f2-dork-02.png)
![Dork 3 — admin without login](dorks/f2-dork-03.png)
![Dork 4 — index of](dorks/f2-dork-04.png)
![Dork 5 — phpinfo, no results](dorks/f2-dork-05.png)
![Dork 6 — sql/bak files](dorks/f2-dork-06.png)

**Note:** dorking your own organisation quarterly is a legitimate, free control. An attacker does not need to break in to read what is already indexed. Microsoft's own surface came back clean across all six dorks, which is itself a useful (negative) result.

---

## Task 3 — Subdomain Reconciliation

Subdomains of a target enumerated with three independent tools, then reconciled into a single deduplicated, resolved list. Each tool draws from different sources, so the union is larger than any single tool.

### Commands

```bash
subfinder -d TARGET -all -silent | sort -u > subs.txt
assetfinder --subs-only TARGET | sort -u >> subs.txt
amass enum -passive -d TARGET -o amass.txt && cat amass.txt >> subs.txt
sort -u subs.txt -o subs.txt
```

![Running subfinder, assetfinder and amass](subdomains/f3-tools-run.png)

### Raw output and the wildcard problem

```bash
wc -l subs.txt     # how many subdomains were found
head -30 subs.txt  # look at the first 30
```

`subs.txt` came back with **205,628 lines**, almost all of it DNS wildcard noise: machine-generated hostnames like `00000000aaaaa....nam.titles.testtse.mos.microsoft.com`, which is a wildcard DNS record resolving every possible subdomain, not real infrastructure.

![Raw subs.txt showing wildcard noise](subdomains/f3-subs-raw.png)

### Cleaning the wildcard noise

```bash
grep -viE '^[0-9a-f]{8}' subs.txt | grep -viE 'testtse|titles\.mos' | sort -u > subs_clean.txt
wc -l subs.txt subs_clean.txt
```

```
205628 subs.txt
204601 subs_clean.txt
410229 total
```

The filter only trimmed ~1,000 lines here, which shows the wildcard pattern wasn't a single clean prefix — a stricter regex or a dedicated wildcard-detection step (e.g. resolving a few random non-existent subdomains and comparing answers) would be needed to fully strip it before this list is usable.

![Deduplicating and filtering wildcard noise](subdomains/f3-subs-dedup.png)

### Resolving live hosts

```bash
shuf subs_real.txt | head -200 | httpx-toolkit -sc -title -ip | head -40
```

A random 200-host sample was checked with `httpx-toolkit` for live status codes, titles and IPs — confirming real, reachable services (e.g. `dining.microsoft.com` → 200, "Cafés – Microsoft Dining & Wellness") mixed with dead or access-denied hosts (403/404/502).

![httpx-toolkit resolving live hosts](subdomains/f3-httpx-resolve.png)

### Reconciliation

| Host | subfinder | assetfinder | amass | Live? |
|---|:---:|:---:|:---:|---|
| www.microsoft.com | ✓ | ✓ | ✓ | 200 |
| support.microsoft.com | ✓ | — | ✓ | 200 |
| learn.microsoft.com | ✓ | — | — | 301 |

In this run `amass` timed out and returned nothing, so `subfinder` and `assetfinder` (passive sources — certificate transparency and DNS aggregators) supplied the entire list. This is exactly why multiple tools are used: relying on `amass` alone here would have found zero hosts. The raw output also contained the DNS wildcard noise shown above, which had to be filtered before resolving. The union of independent tools, minus false positives, gives broader and cleaner coverage than any single tool.

---

## Task 4 — Scanner Comparison: ZAP vs Burp

Both run against the same DVWA target (`192.168.37.132`).

| Aspect | OWASP ZAP | Burp Suite Community |
|---|---|---|
| Licence / cost | Open source, fully free | Free, but automated crawl-and-audit is restricted |
| Automated scanning | Full active scan + AJAX spider for SPAs | Proxy, Repeater, Decoder full; automated audit limited |
| Best for here | Complete free automated coverage | Manual request tampering (Repeater / Intruder-lite) |
| Findings it caught | 12 alert types (see below) | DVWA login request captured and inspected manually |

### ZAP — active scan alerts

ZAP's free active scanner flagged **12 alert types** automatically, including PHP version disclosure with known vulnerabilities, missing CSP/anti-clickjacking/anti-CSRF headers, cookies without `HttpOnly`/`SameSite`, and server version leaks via `X-Powered-By`/`Server` headers.

![ZAP alerts panel](scanners/f4-zap-alerts.png)

### Burp Community — manual request inspection

With Burp Community's automated audit disabled, the DVWA login flow was captured through the proxy instead: a `POST /dvwa/login.php` request showing the session cookie (`security=high; PHPSESSID=...`) and form body (`username=&password=&Login=Login`), ready for manual tampering in Repeater.

![Burp request/response — DVWA login](scanners/f4-burp-request.png)

ZAP found more automatically because its active scanner is free and complete, while Burp Community's automated scanner is disabled — active scanning requires Burp Professional. So for free automated breadth, ZAP wins; for precise manual testing of a single request, Burp's Repeater is the better tool. A spider also under-reports a JavaScript-heavy single-page app, which is why ZAP's AJAX spider exists.

---

## References

- OWASP Web Security Testing Guide (WSTG) — https://owasp.org/www-project-web-security-testing-guide/
- PayloadsAllTheThings — https://github.com/swisskyrepo/PayloadsAllTheThings
- crt.sh — Certificate Transparency logs — https://crt.sh
- ProjectDiscovery subfinder / httpx — https://github.com/projectdiscovery
- OWASP ZAP — https://www.zaproxy.org/
- PortSwigger Burp Suite — https://portswigger.net/burp
