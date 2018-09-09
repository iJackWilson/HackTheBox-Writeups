# Enumeration

Running an nmap scan shows ports 22 and 80 open.

*nmap -sV -sC -Pn -oA Poison 10.10.10.84*

Browsing to the web interface shows an input box used to view files. This is unrestricted so the /etc/passwd file can be viewed to enumerate users

![Homepage](https://github.com/iJackWilson/HackTheBox-Writeups/Images/Homepage.png)

