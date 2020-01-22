---
title: "Vulnhub - The Necromancer (Lessons learnt for newbies!)"
date: 2019-03-25
categories:
  - Capture The Flag
tags:
  - Vulnhub
---

Had my first hands-on experience with a CTF machine ([The Necromancer:1](https://www.vulnhub.com/entry/the-necromancer-1,154/)) at Vulnhub and here are the lessons/key tools learnt from clearing stages/flags. [Vulnhub](https://www.vulnhub.com/about/) is a great place with many downloadable Vulnerable-ready-to-be-exploited VM images (created with the purpose to help others gain practical knowledge on digital security). With my friend's help (Mr. Google) and all the walkthroughs readily available out there (so many that there is no reason for me to do another walkthrough/guide here!), I had spent a few days following through different people's walkthrough, youtube to see how other's try to tackle each stage/flags and learning them by imitation.

My initial thoughts after hands-on this whole thing was **there is definitely no one sure way of doing things**, it depends on the ability to see things from different angles eg. possible ways to find entry into a system. I'm also mind-blown by how powerful [Kali Linux](https://docs.kali.org/introduction/what-is-kali-linux) is -- a Linux distribution packed with many-many readily available tools needed to do pentesting/security auditing. All the tools that was used to break into the system is already in the Kali Linux. Having the tools is one thing, **knowing when to use them** is the key. I felt that there was a [good share by this writer](http://niiconsulting.com/checkmate/2017/06/a-detail-guide-on-oscp-preparation-from-newbie-to-oscp/) for newbies starting out in Pentesting that helps in this learning journey. He mentioned that **Exploiting a machine is a Systematic Process** and this is really evident in all the guides/walkthrough I followed while practicing on this machine:

    1.  Find the open ports and services running on ports
    2.  Enumerate the services and the machine
    3.  Exploit the correct vulnerability and gain access
    4.  Do proper post exploitation enumeration
    5.  Privilege Escalation

* * *

**TOOLS USED** -- Note: These are not all the commands used to clear this CTF but just a short brief examples of tools used to get root.
*   [**nmap**](#nmap) _network discovery_
*   [**nc/ncat**](#nc) _networking utility_
*   [**wireshark**](#wireshark) _network packet analyzer_
*   [**binwalk** ](#binwalk) _binary file analyzer_
*   [**cewl**](#cewl) _word list generator from online source_
*   [**dirb**](#dirb) _web content scanner_
*   [**gdb**](#gdb) _program debugger (software memory hijacker)_
*   [**aircrack-ng**](#aircrack) _wifi wep/wpa cracker_
*   [**hydra**](#hydra) _brute force dictionary attacker_
<!--more-->


### <a name="nmap"></a> Nmap 
_for network discovery_
```bash
nmap -p- -sU -n -v 192.168.1.106
# -p- 	scan all ports from 1 to 65355
# -sU 	scan UDP ports
# -n 	don't resolve dns
# -v 	verbose mode to get more details
```

**When and Why?**	_Most start to scan for an opening port to 'attack' when they start. This is usually the first step in discovering any possible open doors for entry. Nifty to use **arp-scan -l** to discover a list of ip address first before scanning for the open posts on a single host. For this machine, there was only a single opened port (UDP) thus the option -sU (out of all 65535 ports). There are many more different options (advanced level stuff) eg. **T[1-5]** to  adjust how fast (and noisy) you want your scan to be. **-sS** to go in stealthy._
{: .notice--info}

* * *

### <a name="nc"></a> nc/ncat
_networking utility_
```bash
nc -vlp <port number>
# -v 	verbose mode to get more details
# -l 	listen for inbound
# -p 	port number
```
**When and Why?**	_The Netcat tool is the must-know for all pentesters. There were a few times nc was used in this machine where we need to listen to specific TCP/UDP ports on own machine, or "say things" into target host specific ports eg. pipe echo outputs into this command. Sidenote: If you need to deal with any SSL related stuff, use **Ncat** instead which is the newer version of **nc**._
{: .notice--info}

* * *

### <a name="wireshark"></a> wireshark 
_network packet analyzer_
![]({{ site.url }}{{ site.baseurl }}/assets/images/wireshark-eapol.png)

**When and Why?**	_Another must-know tool. There were a few scenarios where wireshark was needed to watch for traffic. One of the hints to capture a flag was using wireshark to find out that target machine was broadcasting some messages using a specific port to us - using this identified the port number for us to listen in. Another scenario was using wireshark to analyse a **.cap** file where there was a wireless EAP authentication handshake taking place (see screenshot above) the key was subsequently decrypted using one a wireless password cracker._
{: .notice--info}

* * *

### <a name="binwalk"></a>binwalk 
_binary file analyzer_

```bash
binwalk -B <filename>
# -B 	scan to see any hidden files
# -e 	extract common files hidden
```

**When and Why?**	_Useful when trying to 'unbox' files that have files hidden in images through steganography (eg. inside JPEG files)._
{: .notice--info}

* * *

### <a name="cewl"></a>cewl 
_word list generator from online source_

```bash
cewl http://www.url-here.com/something.html -m 4 -d 0 -w wordlist.txt
# -m 4 	look for words that are 4 characters
# -d	how deep the level that you want to 'search' the site
# -w 	write outputs to a file
```

**When and Why?**	_CeWL returns a list of words that can be used for dictionary attack. In this case, there was a hint to look for a keyword related to a 'magical item' so we use this tool to generate our own dictionary words such as against a wiki page of all the magical items ever known or (from some fan-frictions blog/webpage). Its useful to use **uniq** and **tr** unix tools to help get the list of words nicely arranged. Once the "dictionary" is prepared, we could then use other tools such as bruteforce attacks to run through all the words in this dictionary._
{: .notice--info}

* * *

### <a name="dirb"></a>dirb 
_web content scanner_
```bash
dirb http://192.168.1.106/target_website.html wordlist.txt
```

**When and Why?**	_At one point, there was a hint that a certain web page could be accessed but we would need to find out what is that page name (directory). Using DIRB we could enumerate the website directory eg. via dictionary. This tool was used with the **[CeWL](#cewl)** to first generate a list of words. When using this tool, the tool will try to get a HTTP response from accessing each of the webpage. Assuming you have 100 words in your dictionary file, dirb will try to access..._
_eg._
_www.url-here.com/word1_
_www.url-here.com/word2_
_....so on and on..._
_www.url-here.com/word100_
_A successful result would be a 'HTTP 200 OK' response indicating that the keyword that was used was a valid webpage._
{: .notice--info}

* * *

### <a name="gdb"></a>gdb 
_program debugger (software memory hijacker)_
```bash
gdb <binary program name>
```

**When and Why?**	_This tool can change how a program should run normally and you can hack it such that it can run a function that you are not suppose to run. Using this tool, you could see whats inside the program eg. what are the functions available, what is the next executing function, set breakpoints in how the program runs etc etc. There was a function that would display key information which we should not have access to, so we use gdb to go around the normal sequence flow of execution, eg. replacing the next executing function in memory to the function we want to run. Good tutorial here in [youtube-how to use gdb](https://www.youtube.com/watch?v=1ztJsJL4DLM)._
{: .notice--info}

* * *

### <a name="aircrack"></a>aircrack-ng 
_wifi wep/wpa cracker_
```bash
aircrack-ng tcp_dump_file.cap -w wordlist.txt
# -w 	bruteforce using dictionary file
```

**When and Why?**	_A traffic dump file was provided at a stage and using wireshark reveals that the was a 4-way EAP authentication handshake being carried out.
![]({{ site.url }}{{ site.baseurl }}/assets/images/wireshark-eapol.png)
Using aircrack, we were able to retrieve the password being used against a dictionary file available in **/usr/share/wordlists/rockyou.txt**_
{: .notice--info}

* * *

### <a name="hydra"></a>hydra 
_brute force dictionary attacker_
```bash
hydra -l loginname -P wordlist.txt 192.168.1.106 ssh
# -l	single login name<
# -P	provide the passwords used to bruteforce
# "ssh"	specify authentication method 
```

**When and Why?**	_I was given a single login name for ssh but does not have the password details, using hydra I was able to find a matching common password from using the word lists provided in **/usr/share/wordlists/rockyou.txt** which gave me a password '12345678'. I believe there are many other tools such as John the Ripper, Medusa etc etc. You can use tools like this to perform Bruteforce attacks, Dictionary attacks, Rainbow table attacks._
{: .notice--info}
