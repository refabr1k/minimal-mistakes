---
title: "My OSCP Experience (I TRIED HARDER!!)"
date: 2020-01-30
categories:
  - Learning
tags:
  - OSCP
---

I TRIED HARDER! Passing [Offensive Security Certified Professional (OSCP)](https://www.offensive-security.com/pwk-oscp/) is a milestone in my life and I hope to share my OSCP journey and hope it will help (or inspire) anyone who is trying to pursue it! The exam is **HARD** and the hardest exam I've ever done - spending more than about 18 hours hacking straight was tough (out of the 5 machines I rooted 3 boxes and managed to get low-privilege user access on the 4th one, with nothing for the last one). I'm thankful to have received many useful tips that have helped me pass this exam and hope this blog post helps pass it on. 

# FIRST SHELL

About 1 year or less ago, I knew nothing about penetration testing or what a getting-a-shell was. (*RCE is just the worst kind of thing that can happen to anyone/company/application in Security context and getting a shell is like that, basically Pwn the system, gaining entry into system servers*). I am in no way a seasoned or very technically skilled in Infosec matters, although I dabble abit in software development in some Java a few years and then worked as a system administrator touching Unix systems, issuing commands on terminal were comfortable for me, I think it did helped me with catching up the courses eg. writing bash scripts etc.

I remember that all I wanted was to better my circumstances (and to get a break from drag-my-feat-to-work kind of job) and follow my desire of working in a security role. I started studying for CEH (Certified Ethical Hacker) which at that had been a 3 year goal now and decided I should finally get into it (The book I bought was version 8 and theres even version 10 now!). I also got myself an ebook [Georgia Weidman's Introduction to Penetration Testing](2019-04-13-post-gwpentesting1.md) after reading some reviews about it online.

This has to be the best hands-on book for beginning pentesting! After following and doing the labs, I got my first Shell experience (hacking remotely) into a windows machine (Exploiting the MS08-067 using metasploit on a Windows XP) and felt like a winner. Its an addictive feeling, getting a shell! I later learnt about Capture-The-Flag machines (like in [vulnhub]([https://www.vulnhub.com/](https://www.vulnhub.com/)) that are designed to be vulnerable for hacking practice. At this point, hacking really got me excited but most importantly the idea that you could hack (legally) and get paid to do it for companies (Penetration Testing) became a career goal for me. **In the process of finding vulnerabilities in computer systems and exploiting them (or showing how to) so that the company would be able to become more secure** is the reason why I want to do this. And its fun too!

Tip: *Good to read [Georgia Weidman's Introduction to Penetration Testing](2019-04-13-post-gwpentesting1.md) (but not a must!) before starting OSCP*
Tip: *This will help for dealing with Linux [wargames bandit](https://overthewire.org/wargames/bandit/)*
Tip: *Good to do vulnerable machines like [Vulnhub](https://www.vulnhub.com/)/[Hack The Box](https://www.hackthebox.eu/) listed in [TJnull's OSCP blog post](https://www.netsecfocus.com/oscp/2019/03/29/The\_Journey\_to\_Try\_Harder-\_TJNulls\_Preparation\_Guide\_for\_PWK\_OSCP.html\#vulnerable-machines)*
Tip: *Good bloggers that inspired me to do OSCP - [hakluke](https://medium.com/@hakluke/haklukes-ultimate-oscp-guide-part-1-is-oscp-for-you-b57cbcce7440), [James Hall](https://411hall.github.io/OSCP-Preparation/), [Abatchy](https://www.abatchy.com/2017/03/how-to-prepare-for-pwkoscp-noob), [KongWenBin](https://kongwenbin.wordpress.com/2017/02/23/officially-oscp-certified/)*

# OSCP Start

After sleeping on the idea of starting OSCP for a while (*what if i fail? if buy labs and time runs out? should i hold it until i become more Pro/skillful?*) I finally YOLO and signed up for the course **90days lab + exam** and expected to start the labs Right away, but damn there was a wait time (about 1 month). After the wait, I finally got the download link for videos/course materials in my email, at this point your labs are also active which means you could login and start hacking away. 

Tip: *If you are planning to start OSCP, give yourself some buffer time to start the course/labs*

I read so many blogs saying that you should complete the videos/labs first before starting the labs - I did just that and perhaps too literal. Going through every detail in the course material, watching the videos and doing the course work to make sure I left out nothing, eg. If i found something I didn't understood I googled and read up and figured it out. Tinker with it on VM and test it on my own. I wrote notes and document them all in **OneNote** (so I could also access them on my phone and read them if I need to). If you are thinking which note taking app should you use? Up to you! But If I could turn back time, I'll use **CherryTree** notes to take my notes for the Exam and explain why later. So I did not touch my labs until 1.5months in (yes totally regretted it)... Being a father of 2 kids and trying to learn something new is difficult and time consuming, besides I am also not a genius. 

Something that helped me greatly when doing OSCP was syncing my folder (containing all workings/notes/exploit/scripts) with cloud storage like google drive. You could use external storages but I find cloud storages the safest in case the external storages goes wonky or worst, missing. I also used the provided Kali linux vmware and try not to update it. [It was mentioned there is no need to update the VM](https://support.offensive-security.com/pwk-kali-vm/) But if you did and your whole kali somehow messes up, you could always scrap the VM and spin up a new one. And since you can add back your google drive folder as shared folder in your kali linux, you can access them again.

Tip: *Use a good note taking tool like CherryTree which allows you to import/export templates for formating your lab/exam reports easily*

Tip: *Use cloud storage like google drive to store your workings/notes and mount them as shared folders with your kali linux*

# A LITTLE HELP FROM FRIENDS

Getting OSCP was not possible without help from the great community and people who willing share knowledge through blogs, talks, videos, social media. I was also fortunate to have some people around that could help nudge me the right direction telling me that I'm in the right track. Since I had placed my resume online and trying to find employment in the Infosec, there was a certain season hacking veteran named **P**  had me come to his office where he and his colleagues **S** and **C** shared some tips and tricks. I learnt alot from them and because of them I had started getting more roots and worked on my methodology and enumeration. Having left about 45days left in my labs I had set my goal to do 1 box/day. You will feel tempted to check forums for hints or use kernel-exploits-quick-wins. Its best if you could find the 'intended' way to root a specific box! Not only will you become more confident after that but it helps you frame in your mind **what you did led to** getting it so you could do it next time, and what didn't worked. I went down "rabbit holes" all the time, and spent days on just 1 box. So I did not get 1 box/day and reading reddit posts about "I was a security professional that started OSCP, just got 20 box in 10 days" made me felt like shit. But hey! You are not running a race but good for them because **It's not about how many boxes you can Root! Approach every box with a mindset that there is a lesson to be learnt!** Sure some of the machines May be old and outdated but thats not the point. 

Join the [Discord channel](https://discord.gg/2AG6TCm) and surround yourself with like minded people. Ask questions Mods/fellow students whats their take on a difficulty you faced. If you were really temped like me and went to forums, You got a nudge and rooted the box, theres no shame. I gotten some nudge in the right direction for the big 4 boxes (pain/sufferance/humble/ghost) Rooted 'em all but wouldn't had if not for some help! Document the "secret sauce" for each situation and build a knowledge bank eg. prepare a cheat sheet ([heres mine](https://refabr1k.gitbook.io/oscp/)) for your exam or current/future Infosec role. 

# EXTENDED 1 MONTH LAB

I extended





About 1 month ago, I received an email notification that I had passed the Offensive Security Certified Professional (OSCP) exam. 
