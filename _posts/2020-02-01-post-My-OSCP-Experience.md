---
title: "My OSCP Experience & Tips (I TRIED HARDER!!)"
date: 2020-02-01
categories:
  - Learning
tags:
  - OSCP
---

I TRIED HARDER! Passing [Offensive Security Certified Professional (OSCP)](https://www.offensive-security.com/pwk-oscp/) is a milestone in my life and I hope to share my OSCP journey and hope it will help (or inspire) anyone who is trying to pursue it! The exam is **HARD** and the hardest exam I've ever done - spending more than about 18 hours hacking was tough (out of the 5 machines I rooted 3 boxes and managed to get low-privilege user access on the 4th one, with nothing for the last one). I'm thankful to have received many useful tips that have helped me pass this exam and hope this blog post helps pass it on. 

TDLR: [OSCP START](#START)

# FIRST SHELL

About 1 year or less ago, I knew nothing about penetration testing or what a getting-a-shell was. (*RCE is just the worst kind of thing that can happen to anyone/company/application in Security context and getting a shell is like that, basically Pwn the system, gaining entry into system servers*). I am in no way a seasoned or very technically skilled in Infosec matters, although I dabble abit in software development in some Java a few years and then worked as a system administrator touching Unix systems, issuing commands on terminal were comfortable for me, I think it did helped me with catching up the courses eg. writing bash scripts etc.

I remember that all I wanted was to better my circumstances (and to get a break from drag-my-feat-to-work kind of job) and follow my desire of working in a security role. I started studying for CEH (Certified Ethical Hacker) which at that had been a 3 year goal now and decided I should finally get into it (The book I bought was version 8 and theres even version 10 now!). I also got myself an ebook **Georgia Weidman's Introduction to Penetration Testing** after reading some reviews about it online.

This has to be the best hands-on book for beginning pentesting! After following and doing the labs, I got my first Shell experience (hacking remotely) into a windows machine (Exploiting the MS08-067 using metasploit on a Windows XP) and felt like a winner. Its an addictive feeling, getting a shell! I later learnt about Capture-The-Flag machines (like in [VulnHub](https://www.vulnhub.com/)) that are designed to be vulnerable for hacking practice. At this point, hacking really got me excited and the idea that one could hack legally and get paid in a job to do it for companies (penetration testing) became a career goal for me. **In the process of finding vulnerabilities in computer systems and exploiting them (or showing how to) so that the company would be able to become more secure** is the reason why I want to do this. And its fun too!

# <a name="START"></a>OSCP START

After sleeping on the idea of starting OSCP for a while (*what if i fail? if buy labs and time runs out? should i hold it until i become more Pro/skillful?*) I finally YOLO and signed up for the course **90days lab + exam** and expected to start the labs Right away, but damn there was a wait time (about 1 month). After the wait, I finally got the download link for videos/course materials in my email, at this point your labs are also active which means you could login and start hacking away. 

Tip: *If you are planning to start OSCP, give yourself some buffer time to start the course/labs*

I read so many blogs saying that you should complete the videos/labs first before starting the labs - I did just that and perhaps too literal. Going through every detail in the course material, watching the videos and doing the course work to make sure I left out nothing, eg. If i found something I didn't understood I googled and read up and figured it out. Tinker with it on VM and test it on my own. 

A good thing to maximise your time with labs is to start automating you scans by using tools like [AutoRecon](https://github.com/Tib3rius/AutoRecon), [OneTwoPunch](https://github.com/superkojiman/onetwopunch). I used run OneTwoPunch with some bash script for loop with all the ip address I discovered and left them running on the background, while working on course materials. So if you Do work a machine, don't waste time runnning a `nmap` scan while staring at it waiting for the scan to finish. I also later used AutoRecon during my exam and Highly recommend it.

I wrote notes and document them all in **[OneNote](https://www.onenote.com/)** (so I could also access them on my phone and read them if I need to). If you are thinking which note taking app should you use? Up to you! But If I could turn back time, I'll use **[CherryTree](https://www.giuspen.com/cherrytree/)** notes to take my notes for the Exam and explain why later. So I did not touch my labs until 1.5months in (yes totally regretted it)... Being a father of 2 kids and trying to learn something new is difficult and time consuming, besides I am also not a genius. 

Something that helped me greatly when doing OSCP was syncing my folder (containing all workings/notes/exploit/scripts) with cloud storage like google drive. You could use external storages but I find cloud storages the safest in case the external storages goes wonky or worst, missing. I also used the provided Kali linux vmware and try not to update it. [It was mentioned there is no need to update the VM](https://support.offensive-security.com/pwk-kali-vm/) But if you did and your whole kali somehow messes up, you could always scrap the VM and spin up a new one. And since you can add back your google drive folder as shared folder in your kali linux, you can access them again.

Tip: *Good to read **Georgia Weidman's Introduction to Penetration Testing** (but not a must!) before starting OSCP*

Tip: *This will help for dealing with Linux [wargames bandit](https://overthewire.org/wargames/bandit/)*

Tip: *Good to do vulnerable machines like [Vulnhub](https://www.vulnhub.com/)/[Hack The Box](https://www.hackthebox.eu/) listed in [TJnull's OSCP blog post](https://www.netsecfocus.com/oscp/2019/03/29/The_Journey_to_Try_Harder-_TJNulls_Preparation_Guide_for_PWK_OSCP.html)*

Tip: *Good bloggers that inspired me to do OSCP - [hakluke](https://medium.com/@hakluke/haklukes-ultimate-oscp-guide-part-1-is-oscp-for-you-b57cbcce7440), [James Hall](https://411hall.github.io/OSCP-Preparation/), [Abatchy](https://www.abatchy.com/2017/03/how-to-prepare-for-pwkoscp-noob), [KongWenBin](https://kongwenbin.wordpress.com/2017/02/23/officially-oscp-certified/)*

Tip: *Use a good note taking tool like CherryTree which allows you to import/export templates for formating your lab/exam reports easily*

Tip: *Use cloud storage like google drive to store your workings/notes and mount them as shared folders with your kali linux*

# A LITTLE HELP FROM FRIENDS

Getting OSCP was not possible without help from the great community and people who willing share knowledge through blogs, talks, videos, social media. I was also fortunate to have some people around that could help nudge me in the right direction and occasionally telling me that I'm on the right track. 

Since I had placed my resume online and trying to find employment in the Infosec, there was a certain seasoned hacking veteran named **P** had me come to his office where he and his colleagues **S** and **C** shared some tips and tricks. I learnt alot from them and because of them I had started getting more roots and worked on my methodology and enumeration. Having left about 45days left in my labs I had set my goal to do 1 box/day. You will feel tempted to check forums for hints or use kernel-exploits-quick-wins. Its best if you could find the 'intended' way to root a specific box! Not only will you become more confident after that but it also helps you tweak your own test routines and checklists, eg what to look out for and what worked or didn't worked in a systematic approach. I went down "rabbit holes" all the time, and spent days on just 1 box. So I did not get 1 box/day and reading reddit posts about "I was a security professional that started OSCP, just got 20 box in 10 days" made me felt like shit. But hey! You are not running a race but good for them because **It's not about how many boxes you can Root! Approach every box with a mindset that there is a lesson to be learnt!** 

Join the [Discord channel](https://discord.gg/2AG6TCm) and surround yourself with like minded people. Ask questions Mods/fellow students whats their take on a difficulty you faced. You may be really tempted like me to go straight to forums looking for a hint, but don't do it unless you have really put in effort and tried everything. But even if you got a nudge and rooted the box, theres no shame because a friend once said "a win is a win bro". I gotten some nudge in the right direction for the big 4 boxes (pain/sufferance/humble/ghost) Rooted 'em all but wouldn't had if not for some help! Because sometimes you don't know what you don't know! But remember to document the "secret sauce" for each situation and build a knowledge bank eg. prepare a cheat sheet ([heres mine](https://refabr1k.gitbook.io/oscp/)) for your exam or current/future Infosec role. 

# EXTENDED 1 MONTH LAB
Unfortunately due to my inability to commit hacking with **P** in his office, I decided to pursue it on my own pace. My lab had ran out after the remaining 45 days where I got about 25 machines. I extended my labs for 30days. There were people saying I should have attempted the exam as my original 90days lab came with 1 exam attempt. But my pride didn't allow because I told myself I want to root them all. I continued and managed to Root all the Public network machines in lab except about 2 machines which has some **dependencies** with machines in other networks. There are total of 45 public machines (3 of them duplicate machines and 1 named 'Alpha' which has a writeup by the legend g0tm1lk in the forums). One of the best thing to do is try rooting 'Alpha' yourself and then go to forums and see g0tm1lk's take on how to root it. Understand his thought process. How he approach every thing is just insightful. He also wrote the [Linux Privesc guide](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/) which I think Every OSCP candidate would have read/use it at least once before, and you should too.

Tip: *If you happen to finish your lab time, try taking the exam because there it will be a rewarding experience even if you don't pass. Say you do fail, and decided to extend the lab, [you will get another complimentary exam attempt](https://www.offensive-security.com/faq/#is-exam-included-extension). You should also be aware that there is now a [Cooling off period](https://www.offensive-security.com/faq/#exam-retake-policy) where you need to wait some time before attempting another exam)* 

The cool thing about the OSCP labs is that it is modeled like an organisation (with different subnet/networks eg. Public network, IT network, Admin network etc). I really enjoyed hacking those machines that require you to **Pivot** from a compromised machine to another machine. This means you will practice on your Post-Exploitation techniques as well eg. what kind of juicy information you can get out of the rooted box. There are some machines that have **dependencies** though, which means you May not hack them until much later stages, when you have gotten something from somewhere, the lesson here is to know when to stop and move on. I remember trying to hack each IP address sequentially in ascending order and didn't want to move on until I hacked it. Don't be like me. You don't have to hack in sequence. Know when to move on.

Tip: *Do post-exploitation and document all juicy info, keys hashes, passwords, notes, mails, data dumps. They will be useful if you happen to discover some machines has some relationship with your rooted machines and the loot you got* 

# EXAM PREP
Feeling salty that I did not root 100% of lab machines, my lab time had ran out and I didn't felt like spending $ to extend the labs. But instead, paid $10 (pounds) to signup for HackTheBox VIP subscription so I could hack the retired boxes and did most of [TJnull's OSCP-like machine list](https://www.netsecfocus.com/oscp/2019/03/29/The_Journey_to_Try_Harder-_TJNulls_Preparation_Guide_for_PWK_OSCP.html#vulnerable-machines) in HackThBox and Vulnhubs. There's plenty of writeups available and watching [IppSec](https://ippsec.rocks) helps! In my opinion, IppSec is a master of his craft, you should watch and learn how he does it! I then practiced Windows Privilege Escalation by practicing with [sagishahar lpeworkshop](https://github.com/sagishahar/lpeworkshop). Practiced buffer overflow using [this awesome collection of buffer overflow applications](https://github.com/freddiebarrsmith/Buffer-Overflow-Exploit-Development-Practice). 

Tip: *Do TJNull's OSCP-like boxes and keep learning. Give yourself a time-limit to hack each one. Then later review what you did against other's writeups, watch IppSec and discover new techniques and ideas of rooting the box.*

# EXAM DAY
The exam duration is 23h 45mins and made sure I read the [OSCP exam guide](https://support.offensive-security.com/oscp-exam-guide/) doubly and triple times beforehand incase anything was missed out or forgotten. I used OBS Recorder for video recording as I was afraid to miss out any screenshots for reports. The exam has 5 machines that I need to get root/admin access, total adding up the score 100 points. The breakdown score for each machines are 25 (buffer overflow), 25, 20, 20, 10 points respectively. You should get the buffer overflow machine in less than 1-2hrs if you practiced it as exactly how it was taught in the course. 

My plan was simply to work on the buffer overflow machine while I run [AutoRecon](https://github.com/Tib3rius/AutoRecon) to scan the other 4 machines. After the scans completed and my Buffer overflow is done, It would be perfect to work on them with the scan outputs all ready for me.

And I had a target timeline like this: 

1000-0100hrs (15hrs) - hack all 

0100-0700hrs (6hrs) - sleep sweet 

0700-1000hrs (3hrs) - last effort and to check flags submitted/recorded/screenshot.

The actual was:
1000-0200hrs (16hrs) - about 67.5 points only (rooted 2 box and 2 users)
I was tired and exhausted, but didn't want to stop. I had been trying to compile an exploit code unsuccessfully. It was a privesc method for a 20pointer box, but sinking feelings set in telling me I had been on a rabbit hole all along. Definitely feeling like giving up and thinking I could extend my labs again and get the 2nd attempt. I tweaked something I was working on and give it a last shot and couldn't believe that it worked, and it did a privesc beautifully, getting the flag after that. 

Because this brings my total score over the passing 70 marks, I celebrated and even teared a little with joy. I later realised that I had been careless and did something wrong without knowing it all along. Since I can't share in details how are the exam machines like but easy-wins like Kernel exploits didn't work. Alot of the things found that led to root could be missed out if enumeration wasn't done properly (just like the labs). Don't overthink too much and keep trying harder and **Know when to move on** when you don't get anything for too long. It was already 0300hrs and after hacking 17 hours straight at this point and felt totally exhasted. Told the proctor I'm going to nap and went to bed. I woke up after about 4.5 hrs and spent the remaining time trying to get just 1 more flag but got nothing. So I made sure all the flags and screens where captured. It is important to do `ifconfig` or `ipconfig`, `cat root.txt` or `type root.txt`, `whoami` all in 1 screenshot the moment you get the flags and immediately submit the flags in the exam dashboard. After 23hr 30mins later, I thanked the proctor and said I wanted to end the exam.

Tip: *Take many breaks, listen to music (the proctorer cant hear you but only see you), plan your meals, drink lots of water, NEVER GIVE UP!*

Tip: *Move on when you can't get anything and revisit it later, who knows for all you know you could be in a 'rabbit hole'?*

# REPORT DAY
After completing the exam I now had to finish my report and submit it within 24hrs. Since my OBS recorder was running, I thought could always go back and get them all screens now for the writeup. But it was a nightmare for me because I didn't **take note of video timestamp for each major step**. Since I work on different boxes at the same time (jumping all over the place), I wasted alot of time when doing the report trying to skim through my video (16+ hours long video!) and trace back my steps. For the 2 machine which I didn't get root, I nevertheless still reported the vulnerability findings and recommended actions to prevent security compromise and also **what I would have done if there was enough time to continue pentesting**. There are no requirements that said you have to do so, but I assumed the role of a Real penetration tester giving a assessment report for my client's system. I thought who knows maybe I could score some brownie-points (these are purely my thoughts). Finally, made sure to double check the report and [submission instructions](https://support.offensive-security.com/oscp-exam-guide/#section-3-submission-instructions) ensuring all things are in order. I finally submitted the report.

Tip: *If you do video recording, make notes of timestamps each major steps eg. at 2hrs 15mins found a vuln that lead to privesc for box 1*

Tip: *capture screen showing ip address, flag, whoami immediately when you get flags, submit them.*

Tip: *Prepare your lab all your course + 10 lab writeups to get additional 5 bonus points, you will get a peace of mind knowing you stand a higher chance passing the exam if you were just missing 5 or less points to hit passing score.*

# RESULTS
Shortly after just few days of submission, I was pleasantly surprised that I had received email notification informing that I had passed Offensive Security Certified Professional (OSCP) on the first attempt. The evaluation duration was really fast, and I'm guessing also because I didn't submit my lab+course work (bonus 5 points) so evaluating my results probably got faster (just my thoughts). 

I'm glad to have walked the OSCP journey and will always remember this milestone in my life: I Tried Harder! If you are doing OSCP and have any questions feel free to contact me at discord (refabr1k#1038) or [email](mailto:refabriksec@gmail.com).

