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

> Something I found helpful when working a room is to create a README for every room containing basic information
> and steps you took. It's useful if you ever enter a new room and face a similar problem. I'll attach mine in the Tech_Supp0rt:1 folder.

if you ever want to do the same, you can insert this command in the terminal to create a file in Sublime Text. 
```
subl README.md
```
> Another helpful tip is to output your nmap result to a file for readability. I did so by creating a directory and outputing
> to a file named inital by running the following commands.
```
mkdir nmap
nmap -sC -sV -oN [file to output to] [targetip]
```
> -sC default script | -sV probe open ports to determine service/version info | -oN output scan in normal

Here's a snippet of the output that was returned. 
```
Starting Nmap 7.60 ( https://nmap.org ) at 2022-06-13 21:34 BST
Nmap scan report for ip-XX-XX-XX-XXX.eu-west-1.compute.internal (XX.XX.XX.XXX)
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
```
Hmmm.. Let's checkout that webpage by inserting [ipaddress]:80 in my web browser.

![image](https://user-images.githubusercontent.com/36011916/173442865-21a79d8e-541e-48ff-9cae-d26a32c47047.png)

> view page source by right clicking or using ctrl + u
> check if there's any hidden elements first

I couldn't find any so, I opted to use gobuster. 
> Gobuster is a tool used to bruteforce URIs - this will help us find any hidden directories
> Gobuster needs both a url and a wordlist.


THM provides a few wordlists in the "Tool" directory:
```
root~# cd Tools/wordlists
root:~/Tools/wordlists# ls
dirb       fasttrack.txt   PythonForPentesters  SecLists
dirbuster  MetasploitRoom  rockyou.txt          wordlists.zip

```
Lets try a few of those and see if we get a hit. 
```
gobuster dir -u [targetip] -w [path to wordlist]
```
Using the directory-list-2.3-medium.txt in the dirbuster directory, i was able to find 3 directories
```
/wordpress

/test

/server-status
```
Along with webserver port open, we also had 139 and 445 open. Let's pivot quickly and try some SMB enumeration.

> smbclient allows us to see whats on the shared samba drives.
> The -L flag lists the shares provided by the IP address.
```
smbclient -L [targetip]
```
It asks for a password to the workgroup. Since we dont yet have that so I hit enter and was returned the following
```
Sharename       Type      Comment
---------       ----      -------
	print$          Disk      Printer Drivers
	websvr          Disk      
	IPC$            IPC       IPC Service (TechSupport server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.

	Server               Comment
	---------            -------

	Workgroup            Master
	---------            -------
	WORKGROUP  
```

Lets see what's shared on the websvr within the workgroup
```
smbclient \\\\[target-ip]\\websvr
```
Hit enter again when it asks for a password. Then run the dir command to see what we can see.
Here's a snippet of the output
```
root:~# smbclient \\\\[targetip]\\websvr
WARNING: The "syslog" option is deprecated
Enter WORKGROUP\root's password: 
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Sat May 29 08:17:38 2021
  ..                                  D        0  Sat May 29 08:03:47 2021
  enter.txt                           N      273  Sat May 29 08:17:38 2021

		8460484 blocks of size 1024. 5678248 blocks available
smb: \> 
```
What's enter.txt? Let't take a look. In the smbclient terminal you can the following command to retrieve the file.
```
get enter.txt
```
> Ctrl+C to exit smbclient. Then run the ls command and that textfile should be in your home directory. Use subl or cat to open it and view its contents. 
![image](https://user-images.githubusercontent.com/36011916/173634923-e051efce-6fac-4cac-b8a4-cd52b99e5de0.png)
Hmm.. fake popup? Could this be a paper trail from our scammer? Are those admin credentials?  

Lets navigate back to those web domains we found earlier and see what we can find..

Visiting the /test directory we're met with this homepage
![image](https://user-images.githubusercontent.com/36011916/173638083-bf932089-5ea1-4e1f-b164-3ca1cb01ca7c.png)
> That must be the fake popup from the list of goals.

After a inspecting the site a bit, seems like its a static site. Nothing important there to enumerate, so lets move on.

Their list of goals mentioned a subrion domain, I attempted to visit that and as the note said.. it didnt work 🙃
But it says something about a panel. Lets just see if adding that to the domain works..
![image](https://user-images.githubusercontent.com/36011916/173657383-035eea34-4b5b-4aa3-aa06-60f9906cd574.png)

Let's see if those credentials from enter.txt works in this panel... nope, what does cooked with magical formula mean?
A few google searches landed me at CyberChef, where they have a Magic operation lets try it out with our credential.
I kept all the default values and got an hit. 
![image](https://user-images.githubusercontent.com/36011916/173659732-09e97db3-92ce-4ba2-8d3f-8ba24dd39b86.png)


I used it and was able to login and see this admin dashboard
![image](https://user-images.githubusercontent.com/36011916/173659899-07b8ece1-d4d1-42ba-b9e7-7488f97f0d7e.png)

After a some investigating, I only found a few empty usergroups (Moderators, Guests, and Registered) as well as a warning about removing the install/modules/module.install.php file. So lets pivot, we know now from the panel that they're using Subrion CMS v4.2.1. Lets find out if there's anything we can do with that intel. 

> SearchSploit is a tool used to view exploit databases.
```
searchsploit subrion
```
Here's what I was returned
```
root:~# searchsploit Subrion CMS 4.2.1
[i] Found (#2): /opt/searchsploit/files_exploits.csv
[i] To remove this message, please edit "/opt/searchsploit/.searchsploit_rc" for "files_exploits.csv" (package_array: exploitdb)

[i] Found (#2): /opt/searchsploit/files_shellcodes.csv
[i] To remove this message, please edit "/opt/searchsploit/.searchsploit_rc" for "files_shellcodes.csv" (package_array: exploitdb)

----------------------------------------------------------------------------------------------------------- ------------
 Exploit Title                                                                                             |  Path
----------------------------------------------------------------------------------------------------------- ------------
Subrion CMS 4.2.1 - Cross-Site Scripting                                                                   | php/webapps/45150.txt
----------------------------------------------------------------------------------------------------------- ------------
Shellcodes: No Results

```
Nice! So now we know, they're suseptible to XSS. I'm already familiar with performing XSS attacks but if you aren't it might be useful to visit (w3schools.com/cybersecurity/cybersecurity_web_applications_attacks.php) or perform a few google searches to get more familiar. 

Next, they mentioned editing the wordpress website..

Visiting the /wordpress directory we're met with this homepage

![image](https://user-images.githubusercontent.com/36011916/173446886-085e441c-2a1a-41cc-8069-4f572420be92.png)


