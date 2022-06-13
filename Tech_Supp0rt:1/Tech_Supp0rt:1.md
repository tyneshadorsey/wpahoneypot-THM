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

if you ever want to insert this command in the terminal
```
subl README.md
```
I run a simple ping to make sure the host is up.

> Another helpful tip is to create output you nmap result to a file for readability.
```
nmap -sC -sV -oN [file to output to]
```
> -sC default script | -sV probe open ports to determine service/version info | -oN output scan in normal
