![Untitled design-2](https://user-images.githubusercontent.com/36011916/173410739-4230f91a-da73-4f8b-9949-2f2bcebdc0ea.png)
# Tech_Supp0rt: 1
Room Link: https://tryhackme.com/room/techsupp0rt1

Writeup By: Ty. Dorsey (@wpahoneypot)

## Challenge
Hack into the scammer's under-development website to foil their plans. 

## Task
Find the root.txt flag 

## Enumeration
Enumeration is incredibly important in pentesting. Nmap should always be your first stop! 
This helps us discover what ports are open as well as provide basic information about any machine.

> Something I found helpful when working a room is to create a README for every room containing basic information,
> and steps you took. It's useful if you ever enter a new room and face a similar problem. I'll attach mine in the Tech_Supp0rt:1 folder.

if you ever want to, insert this command in the terminal
```
subl README.md
```
> Another helpful tip is to output your nmap result to a file for readability. I did so by creating a directory and outputing
> to a file named inital by running the following commands.
```
mkdir nmap
nmap -sC -sV -oN [file to output to] [attackbox_ip]
```
> -sC default script | -sV probe open ports to determine service/version info | -oN output scan in normal

Here's a snippet of the output that was returned. 
```
Starting Nmap 7.60 ( https://nmap.org ) at 2022-06-13 21:34 BST
Nmap scan report for ip-10-10-22-236.eu-west-1.compute.internal (10.10.22.236)
Host is up (0.0063s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 10:8a:f5:72:d7:f9:7e:14:a5:c5:4f:9e:97:8b:3d:58 (RSA)
|   256 7f:10:f5:57:41:3c:71:db:b5:5b:db:75:c9:76:30:5c (ECDSA)
|_  256 6b:4c:23:50:6f:36:00:7c:a6:7c:11:73:c1:a8:60:0c (EdDSA)
80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
MAC Address: 02:66:11:7B:A4:59 (Unknown)
Service Info: Host: TECHSUPPORT; OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
Let's checkout that webpage they have up by opening that page using [ipaddress]:80 in my web browser.

![image](https://user-images.githubusercontent.com/36011916/173442865-21a79d8e-541e-48ff-9cae-d26a32c47047.png)

> view page source by right clicking or using ctrl  = u
> check if there's any hidden elements first

I couldn't find any so, I opted to use gobuster. 
> Gobuster is a tool used to bruteforce URIs - this will help us find any hidden directories
> Gobuster needs both a url and a wordlist.


THM provides a few wordlists in the "Tool" directory:
```
root@ip-10-10-71-98:~# cd Tools/wordlists
root@ip-10-10-71-98:~/Tools/wordlists# ls
dirb       fasttrack.txt   PythonForPentesters  SecLists
dirbuster  MetasploitRoom  rockyou.txt          wordlists.zip

```
Lets try a few of those and see if we get a hit. 
```
gobuster dir -u 10.10.22.236 -w [path to wordlist]
```
Using the directory-list-2.3-medium.txt in the dirbuster directory, i was able to find 3 directories
```
/wordpress

/test

/server-status
```
Lets navigate to those and see what we can find..

Visiting the /wordpress directory we're met with this homepage

![image](https://user-images.githubusercontent.com/36011916/173446886-085e441c-2a1a-41cc-8069-4f572420be92.png)

