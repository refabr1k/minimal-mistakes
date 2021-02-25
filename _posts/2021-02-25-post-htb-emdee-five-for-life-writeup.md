---
title: "HTB - Emdee five for life"
date: 2021-02-25
categories:
  - CTF
tags:
  - Bash
  - Curl
  - Web
---

I enjoyed this challenge for 2 reasons: 
- Learn how to use Curl
- Understand HTTP headers

TLDR version: Its a scripting challenge that requires us to take a hash value, MD5 it and POST it right away. There were some roadblocks along the way that didn't work for me even though I posted the MD5 values using script, and managed to fix it after find out I may have lacked some headers for my POST request.

Start challenge:
The challenge presents itself with some kind of hash value and asks me to MD5 encrypt it. So I put in the `echo <value> | md5sum` output but it always tells me I am slow. This challenge requires scripting to MD5 encrypt the given hash values and submitting it right away.

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/emdee-five/emdee_1.png)

# Curl

 I thought of using curl to find the hash value and than pipe it to a grep command to get the line where theres the hash value for me to MD5. We can see that the line we need has the `<h1>` tags.
 
## Using Curl with other commands


```bash
┌──(kali㉿kali)-[~]
└─$ curl http://167.71.143.20:31051/                                                                                                                                                                                                       
<html>
<head>
<title>emdee five for life</title>
</head>
<body style="background-color:powderblue;">
<h1 align='center'>MD5 encrypt this string</h1><h3 align='center'>RlH5U3VxEdOk7QUH2t9L</h3><center><form action="" method="post">
<input type="text" name="hash" placeholder="MD5" align='center'></input>
</br>
<input type="submit" value="Submit"></input>
</form></center>
</body>
</html>

```

We can add `grep h1` to our command to get that line and extract the values between`<h3>` tags using `sed` (requires some google-fu on some regex).

```bash
┌──(kali㉿kali)-[~]
└─$ curl -s http://167.71.143.20:31051/ | grep h1 | sed -n 's:.*<h3.align.*>\(.*\)</h3>.*:\1:p'                                                                                                                                            
Q7xsvXK2eYvN2YN6K8yj
```

## Similar looking values with different MD5 Sums
Another important note on MD5sum is that the 'invisible' newline character `\n` actually can give us different MD5 results (even though they look so similar!).  This is illustrated below where `echo` the first value (which adds a new line character at the end of the string, and the second `echo -n` which removes any new line character.

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/emdee-five/emdee_md5_note.png)

Now back to our curl command, we can use `tr` to remove new line characters since we only want the absolute values without any newline characters at the back. Then pipe it to `md5sum` to get the values we need. We use the `cut` command to remove the trailing `-`. 

```bash
┌──(kali㉿kali)-[~]
└─$ curl -s http://167.71.143.20:31051/ | grep h1 | sed -n 's:.*<h3.align.*>\(.*\)</h3>.*:\1:p' | tr -d '\n' | md5sum                                                                                                                      
2e7cbdcbf6091023eb92105031347f58  -

┌──(kali㉿kali)-[~]
└─$ curl -s http://167.71.143.20:31051/ | grep h1 | sed -n 's:.*<h3.align.*>\(.*\)</h3>.*:\1:p' | tr -d '\n' | md5sum | cut -d ' ' -f1  > hash.txt                                                                                                    
ffeb8537a50ade47b03675404016532c

```

This is a summary explaination of what the command does:
1. `curl http://167.71.143.20:31051/` - gets me the web page content
2. `grep h1` - gets me the row where the value is given for me to md5sum it
3. `sed` command is just abit of google-fu around in stackover flow that gets the value between <tags></tags>
4. `tr -d '\n'` - deletes any newline characters before I do a md5sum
5. `cut -d ' ' -f1` - cut the whole result string and takes the first part of the string which is before the `-` trail 
6. `> hash.txt` - Write the output into the file `hash.txt`

# HTTP Request Headers
Now we look at how the POST request message is made so we could craft it later. Using burp intercept, we see that the submit field is sent using `hash=` 

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/emdee-five/emdee_burp.png)

I highlighted the things that were worth taking note of like  `Content-Type` and `Cookie` which we will add to our request.

## Content-Type

Now to make a POST request with curl, we can use `curl -d "hash=MY_MD5_HASH_HERE" -X POST http://167.71.143.20:31051/` I also use `--proxy` to send the request over to burp to see how it looks like. In the screenshot below, we I made a POST request with the values of `hash.txt`. Notice that the `Content-Type` was automatically populated. If we want to explicitly change the header we could do so with `-H "Content-Type: xxxxx"`. What about the cookies?

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/emdee-five/emdee_content_type.png)

## Adding Cookie
To add the cookies, we could simply add `-c cookies.txt` to save it as a text file. This is done during my first curl command, then on my next curl to POST I could use the cookies with `-b cookies.txt`. In the below screenshot, we first save the cookies, then use it in the next request.

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/emdee-five/emdee_cookies.png)

# Combining all into a bash script

```bash
#!/bin/bash

curl -s -c cookies.txt http://167.71.143.20:31051/ | grep h1 | sed -n 's:.*<h3.align.*>\(.*\)</h3>.*:\1:p' | tr -d '\n' | md5sum | cut -d ' ' -f1  > hash.txt

curl -d "hash=$(cat hash.txt)" -b cookies.txt http://167.71.143.20:31051/

```

Even though I manage to get this flag but I was unsatisfied, because I had to run the script few times to get it to work. 
![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/emdee-five/emdee_success.png)

Initially I thought perhaps I needed to add the `Connection: keep-alive` In my first curl to 'keep my same session alive' and then subsequently close it at the second. I kinda got better results but still not 100% works every time I run the script. But still great to learn some basics! 

If anyone knows how to get it to Always work, feel free to comment? 