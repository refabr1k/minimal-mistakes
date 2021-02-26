---
title: "HTB - Freelancer"
date: 2021-02-27
categories:
  - CTF
tags:
  - SQLi
  - Burp
  - Web
---

This HTB challenge is great for learning SQL injection! While you could also do it easily with SQLmap, I prefered doing it with Manual approach. Though time consuming but really rewarding and a great learning experience (and refresher for those who had already done OSCP before which was covered in its course materials).

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/freelancer.png)

TLDR version: 
- See something juicy when viewsource 
- Find clues about a particular endpoint which is vulnerable to SQLi
- Exploit SQLi manually to read files/or dump it
- Directory discover admin log in
- Read contents of the login page to find lead to flag


Start challenge:

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_1.png)

When Right Click > View Source, we can find the following endpoint `portfolio.php?id=1`. 

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_2.png)
# Manual SQL Injection
This is our starting point for the SQL injection and notice below that there are some text messages in the response messages (eg. the lorem ipsum messages) gives us good visibility and indicators of how well our SQL injection payload is.
 
![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_sql_1.png)

## Order by - find total columns
I first did `order by <column>` and increasing the count to determine how many columns the table has. If we go beyond the total number of columns (for a union select) this should give an error and we should see a change in the response messages. I also url encode my string (CTRL+U in burp).

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_sql_2.png)

When I exceeded the number of columns (for a union select) I didn't see the chunk of 'lorem ipsum' in response messages which I usually did - this is kind of a good indicater that there tells us some kind of error happened, giving confirmation that there are only 3 columns.

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_sql_3.png)

## Union select

We expand the SQL injection statement by `union select 1,2,3` which should place the values 1, 2 and 3 respectively in the three columns of our database table.

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_sql_4.png)

### Find SQL Version and Current User

Since we can see that the values 2, 3 are shown in our response messages. We can continue using these 2 columns to run our select query statements. We can see the SQL version and current user by using `union select 1,(@@version),user()`.

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_sql_5_version_user.png)

### Current Database 
We can also see the current database by using  `union select 1,2,schema()`. Which shows us that the current database in use is "freelancer".

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_sql_6_database.png)

### Show all Database
As this is a MySQL database, we can show all the databases there is by using  `union select 1,2,schema_name from information_schema.schemata`.  

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_sql_8_all_db.png)

### Show Table names
We can also see what tables there are in the current database (freelancer) using `union select 1,2,table_name from information_schema.tables where table_schema='freelancer'`

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_sql_8_table.png)

### Show Column names of a Table
To see what columns there are in the table "safeadmin" we can do `union select 1,2,column_name from information_schema.columns where table_schema and table_name='safeadmin'`. We see some interested column names such as "username" and "password".

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_sql_9_column.png)

### Show Column values
We have the column names, now we can do a query and select values in the "safeadmin" table using `union select 1,username,password from safeadmin`. Note: I did not spent time cracking this hash and didnt think this was the way to complete it. Its good to know we could also dump database password using SQLMAP (if target is vulnerable to SQLi!) and probably be able to run tools like hashcat or john the ripper to crack the passwords as a kind of commonly known attack vector.

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_sql_10_pw.png)

## Read File
We could also perform read file using `union select 1,2,(select load_file('/etc/passwd')`. The ability to read file is crucial for completing this challenge.

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_sql_7_readfile.png)

# Directory bruteforcing
Perform directory bruteforcing to discover a `/administrat` directory. I used a great tool `ffuf` to do content discovery and the `common.txt` wordlist.

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_3_ffuf.png)

Theres also a `index.php` in this directory. Using the SQL injection vulnerabilty we found, we can read the file content.

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_4_index.png)

We can see that upon authentication, a redirect to `panel.php` occurs. So lets take a look at this file using the same SQL injection vulnerability to read file.

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_5_redirect.png)

Found flag and challenge complete!

![]({{ site.url }}{{ site.baseurl }}/assets/images/htb/synack/freelancer/free_6_found_flag.png)

If you liked this manual exploitation and don't mind more notes/cheatsheets about how to do this again you may refer to https://refabr1k.gitbook.io/oscp/web/sql-injection