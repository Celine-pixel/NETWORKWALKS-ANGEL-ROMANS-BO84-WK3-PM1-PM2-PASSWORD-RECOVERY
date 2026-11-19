# Cracking a Password-Protected PDF with John the Ripper (Johnny GUI on Windows)

A short write-up from the Networkwalks Academy cybersecurity lab: extracting a crackable hash
from an encrypted PDF and recovering the password using **John the Ripper**, run through the
**Johnny** GUI on **Windows** (not Kali Linux).

## Summary

| Item | Detail |
|---|---|
| Target file | `My Locked PDF1.pdf` (password-protected PDF) |
| Tool | John the Ripper 1.9.0-jumbo-1 (via Johnny GUI), Password Cracker, and Hash Calculator|
| Platform | **Windows** (`C:/Users/Celine/Downloads/New folder/john-1.9.0-jumbo-1-win64/run/john.exe`) |
| Attack type | Dictionary attack |
| Hash format | PDF (`$pdf$4*4*128*...`) |
| Cracked password | `password1` |
| Flag captured | `nw{networkwalks_flag1_jtr_270521_1}` |

## Steps

1. **Confirm the PDF is encrypted**
   Opened `My Locked PDF1.pdf` in Microsoft Edge's PDF viewer — it immediately prompted
   for a password ("This file is password protected. Please enter a password to open the file.").

2. **Extract a crackable hash from the PDF**
   Rather than running `pdf2john` from a Linux shell, the hash was pulled out using a
   browser-based extractor (Online HashCrack's PDF hash tool) that implements the same
   `pdf2john` / `hashcat`-compatible output format. The file was parsed and produced a hash
   like:

   ```
   $pdf$4*4*128*-1060*1*16*...*32*...*32*...
   ```

   A second pass through Networkwalks' own "Hash Calculator" tool confirmed the PDF
   was encrypted (Revision 4, Version 4, 128-bit key) and re-extracted the same style of
   `$pdf$...` hash.

3. **Run John the Ripper via the Johnny GUI (on Windows)**
   Instead of using John the Ripper on Kali Linux, the **Windows build** of John the
   Ripper (jumbo version, `john-1.9.0-jumbo-1-win64`) was downloaded and pointed to from
   **Johnny**, the Qt-based GUI front-end for John:
   - `Settings → John the Ripper executable` was set to
     `C:/Users/Celine/Downloads/New folder/john-1.9.0-jumbo-1-win64/run/john.exe`
   - Johnny auto-detected the binary: `John the Ripper 1.9.0-jumbo-1 OMP [cygwin 64-bit x86_64 AVX2 AC]`
   - The extracted `$pdf$...` hash was loaded into Johnny as a password file entry
     (format: `PDF`)
   - A dictionary/wordlist attack was started and run to completion

4. **Password recovered**
   John the Ripper matched the hash against the wordlist and cracked it:

   ```
   [+] MATCH good-luck ✓
   ```

   Johnny's **Passwords** tab showed the cracked value directly against the hash entry.


   Password Cracker matched the hash against the wordlist and cracked it:

   ```
   [+] MATCH password1 ✓
   ```

   Networkwalk Password Cracker **Passwords** tab showed the cracked value directly against the hash entry.


6. **Unlock the PDF**
   Re-opened `My Locked PDF1.pdf`, entered `good-luck` at the password prompt, and the
   document opened — a "Congratulations!" page confirming the flag:

   ```
   nw{cybersecurity_flag_captured_2608}
   ```

   Re-opened `My Locked PDF1.pdf`, entered `password1` at the password prompt, and the
   document opened — a "Congratulations!" page confirming the flag:

   ```
   nw{networkwalks_flag1_jtr_270521_1}
   ```

## Tools used

- **John the Ripper (jumbo, Windows build)** — the actual password-cracking engine
- **Johnny** — GUI front-end for John the Ripper, used instead of the raw CLI
- **pdf2john-compatible hash extractor** (Online HashCrack) — to pull the crackable
  hash out of the encrypted PDF without a Linux environment
- **Networkwalks Hash Calculator** — to pull the crackable hash for the second PDF
- **Networkwalks Password Cracker** — to crack the password for the second PDF
- Microsoft Edge PDF viewer — to verify lock/unlock state of the file

## Note on platform

> This lab is commonly done on **Kali Linux** using the command-line `john` binary plus
> `pdf2john.pl`. In this run, John the Ripper was **not** run on Kali — it was the
> **Windows jumbo build of John the Ripper**, driven through the **Johnny GUI**, with the
> PDF hash extracted via a browser tool instead of the `pdf2john` script. The attack logic
> (dictionary attack against a `$pdf$` hash) is identical either way; only the OS and
> interface differ.

## Repository structure

```
.
├── README.md
└── screenshots/
    ├── 01-password-prompt.png
    ├── 02-flag-variant-a.png
    ├── 03-johnny-cracked-password.png
    ├── 04-johnny-settings-john-exe.png
    ├── 05-onlinehashcrack-extract.png
    ├── 06-networkwalks-hash-calculator.png
    ├── 07-dictionary-attack-lab-start.png
    ├── 08-dictionary-attack-lab-success.png
    └── 09-unlocked-pdf-flag.png
```

## Disclaimer

This was done against a training file explicitly provided by Networkwalks Academy for
educational purposes as part of a cybersecurity course. Do not use these techniques
against files or systems you don't own or have explicit authorization to test.
