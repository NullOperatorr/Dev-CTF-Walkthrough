# Dev-CTF-Walkthrough
CyberLab-13


## Overview  

**Dev** is a vulnerable Linux machine from TCM Security that focuses on web enumeration, service exploitation, credential discovery, and Linux privilege escalation. The goal is to progress from initial reconnaissance to obtaining **root access**.

(https://tcm-sec.com/)

- Operating System:	Linux
- Difficulty:	Medium
- Goal:	Obtain Root Access

**Enviroment:**

- Kali Machine (Attacker).
- Dev (.ovf) VM.
- Make sure both VMs on the same virtual network (NAT).

  ----


## Reconnaissance

- Host Discovery:

```bash
netdiscover -r 192.168.38.0/24
```
<img width="872" height="309" alt="image" src="https://github.com/user-attachments/assets/91da90ed-650a-4f09-83b1-a93384091a11" />


```bash
nmap -sn 192.168.38.0/24
```

<img width="706" height="402" alt="image" src="https://github.com/user-attachments/assets/5ec23507-bbae-4525-be05-bc21993aa8b3" />


We can discover **Dev** via Ping Sweep (nmap) or Arp scan (netdiscover) and the discovered target has IP-Address (192.168.38.140).


---
