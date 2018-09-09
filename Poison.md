# Enumeration

The first step is to run an nmap scan to get an idea of what services are running on the machine.

*nmap -sV -sC -Pn -oA Poison 10.10.10.84*

```
nmap -sV -sC -Pn -oA Poison 10.10.10.84
Starting Nmap 7.70 ( https://nmap.org )
Nmap scan report for 10.10.10.84
Host is up (0.035s latency).
Not shown: 993 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2 (FreeBSD 20161230; protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.29 ((FreeBSD) PHP/5.6.32)
5802/tcp open  http    Bacula http config
5902/tcp open  vnc     VNC (protocol 3.8)
5903/tcp open  vnc     VNC (protocol 3.8)
6002/tcp open  X11     (access denied)
6003/tcp open  X11     (access denied)
Service Info: OS: FreeBSD; CPE: cpe:/o:freebsd:freebsd
```
There's a good amount of services running, but let's start by visiting the web interface which shows an input box used to view files. This is unrestricted so entering `/etc/passwd` will allow us to view this file and enumerate users.

![Homepage](https://github.com/iJackWilson/HackTheBox-Writeups/blob/master/Images/Poison/Homepage.png?raw=true)

We also notice that a file called `listfiles.php` is referenced. Navigating to this page mentions a file called `pwdbackup.txt`

![Homepage](https://github.com/iJackWilson/HackTheBox-Writeups/blob/master/Images/Poison/ListFiles.png?raw=true)

Navigating to this file shows a repeatedly encoded password.

![Homepage](https://github.com/iJackWilson/HackTheBox-Writeups/blob/master/Images/Poison/PwdBackup.png?raw=true)

The = sign at the end of the encoded password is a giveaway that this is Base64 encoded. There are various tools available to decode Base64.

One way to do it would be to use the following bash command, perhaps even putting it inside a for loop:

*echo [base64 encoded value | base64 --decode > pass.txt*

# Connecting to the server

We know from viewing the */etc/passwd* file that there is a user called *Charix* and that SSH is running on port 22, so let's try to connect with the password we just decoded:

*ssh Charix@10.10.10.84*

In my experience, there is some latency with this machine, so you may have to wait 10-15 seconds for the password prompt to appear. Once it does, you have user access on the machine and the ability to grab the user.txt flag.

![Homepage](https://github.com/iJackWilson/HackTheBox-Writeups/blob/master/Images/Poison/UserShell.png?raw=true)

# Privesc

There's also a file called secret.zip sitting in the home directory. We'll need this later so let's copy it using secure copy (SCP). On the host machine (not the HTB machine), type:

`scp Charix@10.10.10.84:~/secret.zip ~/`

This zip is password protected, but can be unzipped with the same password used to log into the server.

Now that we have user privileges, let's escalate to root. Going back to the initial nmap scan, we notice that vns is running on a couple of ports. We can forward the remote vnc port to our local machine using SSH tunnelling:

`ssh -L 6999:localhost:5902 charix@10.10.10.84`

We can now connect to the desktop using vncviewer. There isn't a password to type here, but we use the secret file that we extracted and unzipped earlier as a means of authentication:

`vncviewer 127.0.0.1:6999 -passwd ~/secret`

This should open a vnc session with a terminal visible. We can use this terminal to grab the flag from the root.txt file.