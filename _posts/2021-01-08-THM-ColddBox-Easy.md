---
title: "Try Hack Me: ColddBox: Easy"
date: 2021-01-08
tags: [CTF]
excerpt: ""
---

## CTF Introduction
[Year of the Rabbit](https://tryhackme.com/room/yearoftherabbit) is a Try Hack Me room created by [MuirlandOracle](https://tryhackme.com/p/MuirlandOracle). The room is rated as an easy CTF, despite this, there was quite a bit of work to get to a user shell.

--- 

## Questions
Q1. user.txt

A1. `REDACTED`

Q2. root.txt

A2. `REDACTED`

## Initial Nmap
```
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: WordPress 4.1.31
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: ColddBox | One more machine
```
From the initial Nmap, I see there is a webpage built with WordPress (version 4.1.31) and Apache.

## Webpage
### IP/
The homepage of the website shows a message about this being a simple machine with many ways to escalate privileges. 

As it seems there is a lot to this website, I started up Gobuster. 

<p align="center">
<img src="{{ site.url }}{{ site.baseurl }}/images/THM-ColddBox-Easy/gobuster.png" alt="Gobuster results">
</p>

Gobuster returned a few results, but nothing abnormal for a WordPress site.

While Gobuster was running, I used searchsploit to see any exploits relating to WordPress 4.1.31.

<p align="center">
<img src="{{ site.url }}{{ site.baseurl }}/images/THM-ColddBox-Easy/searchsploit.png" alt="Searchsploit results">
</p>

Searchsploit showed a few promising leads, I'll try enumerating the users and then try an unauthorised password reset.

## WordPress Enumeration
To enumerate the WordPress users, I used `wpscan --url http://{IP} --enumerate u`. This discovered the following users:

<p align="center">
<img src="{{ site.url }}{{ site.baseurl }}/images/THM-ColddBox-Easy/wpscan.png" alt="WPScan user enumeration results">
</p>

Given that this website is by `c0ldd`, I'll try changing the password of this account, in the hopes that it is the webpage admin account.

## WordPress Unauthorized Password Reset
I copied the exploit that searchsploit discovered using `cp /usr/share/exploitdb/exploits/linux/webapps/41963.txt .`. Viewing 

## Summary

<p align="center">
<img src="https://tryhackme-badges.s3.amazonaws.com/cjboyd.png" alt="TryHackMe">
</p>