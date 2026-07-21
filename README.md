# Stuntman Mike Walkthrough – Weak Credentials to Root via Sudo Misconfiguration
<img width="353" height="182" alt="Screenshot 2026-07-21 140943" src="https://github.com/user-attachments/assets/d17212fa-ddf2-4146-aa92-2478a81ff55e" />

> **Difficulty:** Easy
> **Category:** Linux Privilege Escalation
> **Techniques:** Nmap, Hydra, SSH, Linux Enumeration, Sudo Privilege Escalation

## Overview

In this lab, I assessed a Linux target by performing service enumeration, identifying weak SSH credentials through an authorised password audit, and exploiting an overly permissive `sudo` configuration to obtain root access. The exercise highlights how weak passwords and excessive administrative privileges can compromise an entire system.

## Tools Used

* Nmap
* Hydra
* OpenSSH
* Linux Terminal

## Step 1: Reconnaissance

The first step was to identify open ports and running services.

```bash
nmap -sC -sV -O 10.150.150.166
```

### Results
> SSH immediately became the primary attack surface because it allows remote authentication.

<img width="537" height="247" alt="Screenshot 2026-07-21 133332" src="https://github.com/user-attachments/assets/53bdea60-8aa3-4536-80b4-b251c72c2539" />

## Step 2: Password Audit

With SSH exposed, I performed an authorised password audit against the target user.

```bash
hydra -l mike -P /usr/share/wordlists/rockyou.txt ssh://10.150.150.166 -V -f
```

Hydra successfully identified valid SSH credentials.

**Outcome**

<img width="536" height="235" alt="Screenshot 2026-07-21 134445" src="https://github.com/user-attachments/assets/eb5cc510-b6ad-483b-8c91-2bb20ab47081" />


## Step 3: SSH Access

Using the discovered credentials, I connected to the target via SSH.

```bash
ssh mike@10.150.150.166
```
After authenticating successfully, I gained access to the system as the `mike` user.
<img width="536" height="305" alt="Screenshot 2026-07-21 134959" src="https://github.com/user-attachments/assets/26a9f6e3-5ac9-452a-a2b7-19e75b122838" />

I then confirmed the current user:

```bash
whoami
```

and enumerated the contents of the user's home directory.
<img width="536" height="62" alt="Screenshot 2026-07-21 135156" src="https://github.com/user-attachments/assets/74200619-dbf7-491f-a279-dd8393730acb" />

## Step 4: User Enumeration

Once inside the system, I checked for interesting files and verified my permissions.

```bash
whoami
ls
```

This stage also involved reviewing accessible files and continuing host enumeration.

## Step 5: Sudo Enumeration

Privilege escalation on Linux should always begin by checking the user's sudo permissions.

```bash
sudo -l
```
The output showed that the account had unrestricted sudo privileges.
<img width="511" height="178" alt="Screenshot 2026-07-21 140704" src="https://github.com/user-attachments/assets/588ed99a-2e57-48ce-9e79-cdca80cf961a" />

## Step 6: Privilege Escalation

Since unrestricted sudo access was available, obtaining a root shell was straightforward.

```bash
sudo /bin/sh
```

After executing the command:

```bash
whoami
```

returned:

```text
root
```

indicating full administrative access.
<img width="511" height="178" alt="Screenshot 2026-07-21 140704" src="https://github.com/user-attachments/assets/b281a17b-a2f1-481a-9b92-590ae2dd47ef" />
#  Key Takeaways

* Always enumerate all exposed services before attempting exploitation.
* Weak passwords remain one of the most common causes of compromise.
* SSH should always be protected with strong passwords or public-key authentication.
* Regular password auditing helps identify weak credentials before attackers do.
* Misconfigured sudo permissions can completely undermine Linux security.
* Enumerating privileges (`sudo -l`) should always be part of a Linux privilege escalation checklist.

## Disclaimer

This walkthrough is intended **solely for educational purposes** and documents activities performed in an **authorised Capture The Flag (CTF) laboratory environment**. The techniques described should only be used on systems for which you have explicit permission to test.
