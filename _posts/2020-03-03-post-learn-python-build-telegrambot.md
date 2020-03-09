---
title: "Building a Money Tracking Bot (Telegram) from scratch"
date: 2020-03-03
categories:
  - Learning
tags:
  - Python
---

TLDR:
-	Learn python basics from [https://www.hackerrank.com/](https://www.hackerrank.com/)
-	Learn building telegram bots [engineers.sg](https://engineers.sg/episodes/search?search=telegram)
-	Fun telegram bot project source code [https://github.com/refabr1k/MASTER-OF-COIN_telegram_bot](https://github.com/refabr1k/MASTER-OF-COIN_telegram_bot) 

# Python for beginners! 
Python is great! It is clean, efficient and simple to write. [https://www.hackerrank.com/](https://www.hackerrank.com/) is a great place to learn the basics - you get to level up your ranks everytime a challenge is completed. Theres a great discussion sections in each challenges where people share their take on each challenge, eg. You'll be mindblown how these smart people can write your 20 line codes in 2 just lines!) 

![]({{ site.url }}{{ site.baseurl }}/assets/images/hackerrank.png)

You'll get a "Problem" and some given conditions to solve the challenge. Type out the code in the same window in a built-in command line interface in the same browser and click submit to evaluate your code! If you are stuck, you can always view "Discussions" and get some hints from others.


# Learn how to build Telegram Bots in Python! 
I stumbled upon a great follow along video tutorial on [engineers.sg](https://engineers.sg/episodes/search?search=telegram). Theres some basic here how to build a simple Telegram bot. While following along, I was also inspired to try building a Telegram Bot (this is the best way to learn isn't it? Actually code something!)

# Master Of Coin Telegram Bot (built in Python)
I dabbled abit and went with a python API `pyTelegramBotAPI` which is a simple wrapper building bots in python. The idea of what this bot can do is to allow quick and easy way to record expenses! Sure, there are better free and nicer looking mobile apps out there to track expenses with but sometimes it takes just too many taps/clicks to do something as simple as recording how much I ate for lunch or 'Food'. Note: This little bot is not designed or optimized to serve bazzillion or gazzilion users! It was a fun project shared among close friends and family - the amazing thing is you can take this code and customize it anyway you want it that fits your needs. Example the categories used for recording a new spending doesn't suit you, change it! 

![]({{ site.url }}{{ site.baseurl }}/assets/images/telegrambot/start.jpg)

## How to setup?

1. Get api key from BotFather. Simply send `/newbot` to "BotFather" in Telegram and follow the onscreen instructions to get an API key. Place this api key in `"<API_KEY>"` of `coin_bot.py` file.
2. Install the following dependencies: `pyTelegramBotAPI` (eg. run `pip install pyTelegramBotAPI`)
3. Create an empty `data.json` in the same directory of your `coin_bot.py`. All recorded expenses will be stored here.
4. Run `python3 coin_bot.py` 

Note: Ideally the bot should be setup in the cloud or some FaaS instead of running on your local machine so you could still chat your bot when you turn off your machine :D

## How to use?
### Start / Help menu

* Send `/help` command show help menu

![]({{ site.url }}{{ site.baseurl }}/assets/images/telegrambot/start.jpg)

### Record a new expenses

* Send `/new` command and select category
* Key in amount to record spending 

![]({{ site.url }}{{ site.baseurl }}/assets/images/telegrambot/new1.jpg)

![]({{ site.url }}{{ site.baseurl }}/assets/images/telegrambot/new2.jpg)


### Show spendings

* Send `/show` command and select mode (day or month)

![]({{ site.url }}{{ site.baseurl }}/assets/images/telegrambot/show1.jpg)

![]({{ site.url }}{{ site.baseurl }}/assets/images/telegrambot/show2.jpg)

### View all spending history

* Send `/history` command to view all spending history so far

![]({{ site.url }}{{ site.baseurl }}/assets/images/telegrambot/history.jpg)

## For Developers

### Data structure and persisting

`data.json` file is loaded on every read/write action into a global dictionary, with the following json key-value structure

```json
    "CHAT ID 1": [
        "<DD-MMM-YYYY HH:MM>,<CategoryString>,<Amount>",
        "<DD-MMM-YYYY HH:MM>,<CategoryString>,<Amount>",
        ...
        "<DD-MMM-YYYY HH:MM>,<CategoryString>,<Amount>"
    ],
    "CHAT ID 2": [
        "<DD-MMM-YYYY HH:MM>,<CategoryString>,<Amount>",
        "<DD-MMM-YYYY HH:MM>,<CategoryString>,<Amount>",
        ...
        "<DD-MMM-YYYY HH:MM>,<CategoryString>,<Amount>"
    ],
    "CHAT ID 2": [
        "<DD-MMM-YYYY HH:MM>,<CategoryString>,<Amount>",
        "<DD-MMM-YYYY HH:MM>,<CategoryString>,<Amount>",
        ...
        "<DD-MMM-YYYY HH:MM>,<CategoryString>,<Amount>"
    ]
}
```

Keys: String id that uniquely identifies user client.
Values: String arrays comma seperated with date values, category strings, amount

### Query method

The functionality to group category, sum of spending for that category, by day or month is done by

1. Defining query time or date or month

2. Enumerating dictionary key,values for query
   
   ```python
   dateFormat = '%d-%b-%Y'
   timeFormat = '%H:%M'
   monthFormat = '%b-%Y'
   
   #get all string array of ['Date','Category','amount'] for client id
   history = global_dictionary['CHAT_ID']
   
   #prepare query string for specific date/time/month
   query = datetime.now().today().strftime(dateFormat)
   
   #enumerate key values to get a match of string array ['Date','Category','amount']
   result = [value for index, value in enumerate(history) if str(query) in value]
   
   CALCULATE(result)
   ```

3. Suming up all categories
   
   ```python
   def CALCULATE(result)
   catSum = {}
   
        for row in result:

           s = row.split(',') # ['Date','Category','amount']

           cat = s[1]  # 'Category'
           
        # sum grouped category amounts
           if cat in catSum:
               #round up to 2 decimal
               catSum[cat] = round(catSum[cat] + float(s[2]),2)    
           else:
               #first entry
               catSum[cat] = float(s[2])
   ```

This functionality is added because I wanted to have a quick overview the total amount of spending for each category (having a budget in mind) I could tell if I overspent or not. 

Example: If I had spent $4 on breakfast, lunch and dinner respectively (adds up to $12) I could create a query for todays date and show a sum of all category. The result could be formatted in a simple text response from the bot as such:


```bash
Total spending for today:
CATEGORY, AMOUNT
----------------
Food: $12.0
```

### Good features to have and explore
* Query by single date (eg. how much did I spent on that that day again?)
* Delete (made a mistake and want to remove a spending record)
* Set a expenses goal and set notify/alert you if you are overspending
* Email spendings (eg. txt,csv or xls) to yourself




