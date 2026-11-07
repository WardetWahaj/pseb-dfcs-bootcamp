# S01 — Foundations: Malware Research and Linux Practical Work

**Author:** Raja Wardet Wahaj (Wolf)
**Environment:** Kali Linux / Linux terminal; Python 3
**Tasks completed:** Task A, B2, B3

Full write-up: `Day1_Homework_Wolf_v2.docx` (add it to this folder).

---

## Task A — Malware Types and Real-World Examples

Twelve malware types (virus, worm, trojan, ransomware, spyware, adware, rootkit, keylogger, botnet agent, fileless/LOTL, logic bomb, bootkit), each with a definition, real example, spread method and impact.

**Most dangerous for a Pakistani SME: ransomware.** One incident can lock accounting, customer and invoice data at once, and SMEs often have limited IT staff, weak patching and backups on the same network.

---

## Task B2 — Linux File Permissions

```bash
mkdir -p ~/s01-foundations/b2 && cd ~/s01-foundations/b2
printf "This file is for a chmod permissions exercise.\n" > permissions_demo.txt
ls -l permissions_demo.txt

chmod u=rw,g=r,o= permissions_demo.txt   # Change 1: symbolic assignment (640)
chmod o+r permissions_demo.txt           # Change 2: symbolic addition (644)
chmod 600 permissions_demo.txt           # Change 3: octal (600)
stat -c "Octal: %a | Symbolic: %A | File: %n" permissions_demo.txt
```

| Stage | Command | Result |
|---|---|---|
| Change 1 | `chmod u=rw,g=r,o=` | `-rw-r-----` (640) |
| Change 2 | `chmod o+r` | `-rw-r--r--` (644) |
| Change 3 | `chmod 600` | `-rw-------` (600) |

### Evidence

![Initial](evidence/b2-1-initial.png)
![Symbolic 640](evidence/b2-2-symbolic-640.png)
![Symbolic 644](evidence/b2-3-symbolic-644.png)
![Octal 600](evidence/b2-4-octal-600.png)

---

## Task B3 — Caesar Cipher Decoder

Script: [`Scripts/caesar_decoder.py`](Scripts/caesar_decoder.py)

```bash
python3 Scripts/caesar_decoder.py "KDFNHU"
```

Prints all 25 non-zero shifts, preserving case, spaces and punctuation. For `KDFNHU`, shift 3 gives `HACKER`.

![Caesar run](evidence/b3-caesar-run.png)

**Why 25 tries is not security:** the key space is tiny and letter frequencies are preserved, so a computer breaks it instantly.

---

## References

See the references list in the full write-up (.docx).
