# TryHackMe-OverPass Machine
----------------------------------------------------------------------
## 1- Start With Enumeration
#### Scanning the target Machine Using Nmap ==>> `nmap -sC -sV [Target IP]`
```
root@mahmoud:~# nmap -sC -sV 10.10.140.143
Starting Nmap 7.91 ( https://nmap.org ) at 2021-12-23 19:12 EET
Nmap scan report for 10.10.140.143
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
#### Then Let's Brute Force to searching the files of the web server
#### using command ` gobuster dir -u http://10.10.88.200/ -w ~/Lists/gobuster/Gobuster-Dir-Medium.txt -t 30`
### The Result:
```
/img                  (Status: 301) [Size: 0] [--> img/]
/downloads            (Status: 301) [Size: 0] [--> downloads/]
/aboutus              (Status: 301) [Size: 0] [--> aboutus/]
/admin                (Status: 301) [Size: 42] [--> /admin/]
/css                  (Status: 301) [Size: 0] [--> css/]
```
-------------------------------------------------------------------

## Let's Open /admin Dirctory
![Image of mahmoudashraf1344](https://github.com/0x1mahmoud/TryHackMe-Nax/blob/main/img/image-149.webp)
