# DFCS Bootcamp | Atomcamp (PSEB)

![Program](https://img.shields.io/badge/PROGRAM-DFCS%20BOOTCAMP-1F4B99?style=for-the-badge)
![Status](https://img.shields.io/badge/STATUS-IN%20PROGRESS-D97706?style=for-the-badge)

**Cybersecurity Training, Practical Labs, Technical Reports & Projects**

Author: Raja Wardet Wahaj (Wolf)

---

## About This Repository

This repository documents my practical work in the DFCS Bootcamp (Atomcamp, Cohort 2). It is a structured record of labs, scripts, research and evidence, covering cybersecurity fundamentals, malware research, Linux administration, network scanning, vulnerability assessment/penetration testing, client-side attacks and privilege escalation, web reconnaissance and web app pentesting, and Python programming.

---

## Training Progress

| Session | Topic | Status | Documentation |
|---|---|---|---|
| S01 | Foundations: Malware Research, Linux Permissions, Caesar Cipher | **Completed** | [View Session](s01-foundations/README.md) |
| S02 | To be updated | Pending | — |
| S03 | To be updated | Pending | — |
| S04 | To be updated | Pending | — |
| S05 | To be updated | Pending | — |
| S06 | Network, Port and Vulnerability Scanning | **Completed** | [View Session](s06-scanning/README.md) |
| S07 | Vulnerability Assessment & Penetration Testing (Nessus + Metasploit) | **Completed** | [View Session](s07-report/README.md) |
| S08 | Client-Side Attacks: Payloads, Privilege Escalation, Career Paths | **Completed** | [View Session](s08-client-side/README.md) |
| S09 | Recon & Web App Pentesting (Dorking, Subdomains, ZAP vs Burp) | **Completed** | [View Session](s09-recon-and-upload/README.md) |
| S10 onwards | To be updated | Upcoming | — |

*Progress will be updated as the training continues.*

---

## Completed Practical Work

### S01 — Foundations

| Task | Description |
|---|---|
| Task A | Malware types with real-world examples; most dangerous type for a Pakistani SME |
| Task B2 | Linux file permissions using symbolic and octal `chmod` |
| Task B3 | Caesar cipher decoder in Python (all 25 shifts) |

[**View Complete S01 Documentation**](s01-foundations/README.md)

### S06 — Network, Port and Vulnerability Scanning

Lab: Kali Linux (attacker) against Metasploitable 2 (target) in an isolated NAT lab.

| Finding | Identifier | CVSS v3.1 | Severity |
|---|---|:---:|---|
| vsftpd 2.3.4 backdoor | CVE-2011-2523 / CWE-78 | 9.8 | Critical |
| Exposed phpinfo.php | CWE-200 | 5.3 | Medium |
| Telnet service exposed | CWE-319 | 5.9 | Medium |

Also completed: **B3** (CVSS vector decoded) and **B4** (CWE classification of three vulnerabilities).

[**View Complete S06 Documentation**](s06-scanning/README.md)

### S07 — Vulnerability Assessment & Penetration Testing Report

Full grey-box VA/PT engagement against Metasploitable 2: manual enumeration, an automated Tenable Nessus scan, a Metasploit exploitation walkthrough to root via the vsftpd backdoor, and post-exploitation enumeration.

| Finding | CVSS | Severity |
|---|:---:|---|
| vsftpd 2.3.4 backdoor (CVE-2011-2523) | 9.8 | Critical |
| Default credentials (msfadmin) | 8.8 | High |
| Telnet exposed | 5.9 | Medium |
| phpinfo.php disclosure | 5.3 | Medium |

Nessus flagged 24 findings (7 Critical, 2 High, 3 Medium, 1 Low, 11 Info) and agreed with all 4 manually validated findings.

[**View Complete S07 Documentation**](s07-report/README.md)

### S08 — Client-Side Attacks: Payloads and Privilege Escalation

Windows 10 lab: `msfvenom`-generated Meterpreter payload, caught with `multi/handler`, escalated from a filtered Administrator token to `NT AUTHORITY\SYSTEM` after UAC blocked the first `getsystem` attempt, via the `bypassuac_fodhelper` module. Also covers a 9-family malware classification table and a career roadmap (SOC L1 → network/AD penetration testing).

[**View Complete S08 Documentation**](s08-client-side/README.md)

### S09 — Recon & Web App Pentesting

A full scope document and rules of engagement for a hypothetical `*.intel.com` test, six Google dorks run against a live domain, three-tool subdomain reconciliation (subfinder/assetfinder/amass) with wildcard-noise cleanup and live-host resolution via `httpx-toolkit`, and a structural + hands-on comparison of OWASP ZAP vs. Burp Suite Community against DVWA.

[**View Complete S09 Documentation**](s09-recon-and-upload/README.md)

---

## Repository Structure

```
pseb-dfcs-bootcamp/
├── README.md
├── s01-foundations/
│   ├── README.md
│   ├── Scripts/
│   └── evidence/
├── s06-scanning/
│   ├── README.md
│   ├── DFCS_Day2_HomeWork_Raja_Wardet_Wahaj.pdf
│   └── figures/
├── s07-report/
│   ├── README.md
│   ├── DFCS_Day3_HomeWork_Raja_Wardet_Wahaj.docx
│   └── figures/
├── s08-client-side/
│   ├── README.md
│   ├── DFCS_Day4_HomeWork_Raja_Wardet_Wahaj.docx
│   └── figures/
├── s09-recon-and-upload/
│   ├── README.md
│   ├── day5-homework.docx
│   ├── scope/
│   │   └── intel-scope-and-roe.md
│   ├── dorks/
│   ├── subdomains/
│   └── scanners/
└── ...
```

| Directory / File | Purpose |
|---|---|
| `Scripts/` | Scripts and commands developed during the session |
| `README.md` | Session overview, method, findings, conclusion |
| `evidence/`, `figures/`, `dorks/`, `subdomains/`, `scanners/` | Screenshots, logs, outputs, reports |

---

## Tools & Technologies

| Category | Tools |
|---|---|
| Operating Systems | Kali Linux, Metasploitable 2, Windows 10 |
| Network Scanning | Nmap (NSE scripts), tcpdump |
| Vulnerability Management | Tenable Nessus |
| Exploitation & Payloads | Metasploit Framework, msfvenom, Meterpreter |
| Recon & OSINT | Google dorking, subfinder, assetfinder, amass, httpx-toolkit, crt.sh |
| Web App Testing | OWASP ZAP, Burp Suite Community, DVWA |
| Network Utilities | telnet, ftp, ssh |
| Programming | Python 3 |
| Virtualization | VMware |
| Security Research | CVE, CWE, CVSS, NVD, MITRE ATT&CK, OWASP WSTG |
| Documentation | Markdown, Git, GitHub |

---

## Responsible Use & Disclaimer

All exercises are performed for educational purposes in authorized, isolated lab environments (intentionally vulnerable machines or VMs I own), under written course authorization. Google dorking exercises target only publicly indexed information and exploit nothing. Nothing here may be used against systems without explicit permission.

---

*Learning through practice. Building through projects. Improving through documentation.*
