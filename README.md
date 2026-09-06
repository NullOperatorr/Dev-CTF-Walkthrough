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


## Enumeration

**1- Port Scanning:**

```bash
nmap -Pn -sC -sS -sV -p- -T4 192.168.38.140
```

<img width="594" height="691" alt="image" src="https://github.com/user-attachments/assets/3928002f-4dde-4aae-9516-0343b9b6ace9" />  

| Port | Service | Version / Details |
|------|---------|-------------------|
| 22 | SSH | OpenSSH 7.9p1 |
| 80 | HTTP | Apache httpd 2.4.38 |
| 111 | RPC | RPCBind |
| 2049 | NFS | Network File System |
| 8080 | HTTP | Apache httpd 2.4.38 |
| 38005 | RPC | Unknown |
| 45699 | RPC | Unknown |
| 49185 | RPC | Unknown |
| 57209 | RPC | Unknown |


 


