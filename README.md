# DFCS Bootcamp | Atomcamp (PSEB)

![Program](https://img.shields.io/badge/PROGRAM-DFCS%20BOOTCAMP-1F4B99?style=for-the-badge)
![Status](https://img.shields.io/badge/STATUS-IN%20PROGRESS-D97706?style=for-the-badge)

**Cybersecurity Training, Practical Labs, Technical Reports & Projects**

Author: Raja Wardet Wahaj (Wolf)

---

## About This Repository

This repository documents my practical work in the DFCS Bootcamp (Atomcamp, Cohort 2). It is a structured record of labs, scripts, research and evidence, covering cybersecurity fundamentals, malware research, Linux administration, network scanning and Python programming.

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
| S07 onwards | To be updated | Upcoming | — |

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
└── ...
```

| Directory / File | Purpose |
|---|---|
| `Scripts/` | Scripts and commands developed during the session |
| `README.md` | Session overview, method, findings, conclusion |
| `evidence/`, `figures/` | Screenshots, logs, outputs, reports |

---

## Tools & Technologies

| Category | Tools |
|---|---|
| Operating Systems | Kali Linux, Metasploitable 2 |
| Network Scanning | Nmap (NSE scripts), tcpdump |
| Network Utilities | telnet, ftp |
| Programming | Python 3 |
| Virtualization | VMware |
| Security Research | CVE, CWE, CVSS, NVD |
| Documentation | Markdown, Git, GitHub |

---

## Responsible Use & Disclaimer

All exercises are performed for educational purposes in authorized, isolated lab environments (intentionally vulnerable machines). Nothing here may be used against systems without explicit permission.

---

*Learning through practice. Building through projects. Improving through documentation.*
