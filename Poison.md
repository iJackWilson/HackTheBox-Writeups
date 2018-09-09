# Enumeration

Running an nmap scan shows ports 22 and 80 open.

*nmap -sV -sC -Pn -oA Poison 10.10.10.84*

Browsing to the web interface shows an input box used to view files. This is unrestricted so the /etc/passwd file can be viewed to enumerate users

![Homepage](https://github.com/iJackWilson/HackTheBox-Writeups/blob/master/Images/Poison/Homepage.png?raw=true)

We also notice that a file called *listfiles.php* is referenced. Navigating to this page mentions a file called *pwdbackup.txt*

![Homepage](https://github.com/iJackWilson/HackTheBox-Writeups/blob/master/Images/Poison/ListFiles.png?raw=true)

Navigating to this file shows a repeatedly encoded password.

![Homepage](https://github.com/iJackWilson/HackTheBox-Writeups/blob/master/Images/Poison/PwdBackup.png?raw=true)

The = sign at the end of the encoded password is a giveaway that this is Base64 encoded. There are various tools available to decode Base64.

One way to do it would be to use the following bash command, perhaps even putting it inside a for loop:

*echo [base64 encoded value | base64 --decode > pass.txt*

We know from viewing the */etc/passwd* file that there is a user called *Charix* and that SSH is running on port 22, so let's try to connect with the password we just decoded:

*ssh Charix@10.10.10.84*

In my experience, there is some latency with this machine, so you may have to wait 10-15 seconds for the password prompt to appear. Once it does, you have user access on the machine and the ability to grab the user.txt flag.

![Homepage](https://github.com/iJackWilson/HackTheBox-Writeups/blob/master/Images/Poison/UserShell.png?raw=true)