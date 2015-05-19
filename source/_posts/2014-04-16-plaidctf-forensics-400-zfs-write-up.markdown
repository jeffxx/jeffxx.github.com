---
layout: post
title: "PlaidCTF Forensics 400 ZFS write-up"
date: 2014-04-16 21:04:29 +0800
comments: true
categories: [plaid, ctf, writeup, foensics] 
---

這次與217 Orange 組隊打 PlaidCTF 靠超強隊友拿到第五名

記分板非常華麗, 滑鼠移過去可以看到各題型即時統計,得分狀況 很特別的設計.
現在好像還看的到  有興趣的可以看一下

http://play.plaidctf.com/scoreboard

{% img /images/writeup/plaidctf2014_problemboard.png %}

{% img /images/writeup/plaidctf2014_scoreboard.png %}



PPP出的題目非常棒, 像是: 

>二次汙染的 Blind SQL injection - [WhatsCat](https://github.com/ctfs/write-ups/tree/master/plaid-ctf-2014/whatscat)

>從 Memoery dump 挖出 master secret key 解 https - [Curlcore](https://github.com/ctfs/write-ups/tree/master/plaid-ctf-2014/curlcore) 

>跟上潮流的 [Heartbleed](https://github.com/ctfs/write-ups/tree/master/plaid-ctf-2014/heartbleed)

>被埋後門的 [GCC](https://github.com/ctfs/write-ups/tree/master/plaid-ctf-2014/gcc)

>要改Return address 或 GOT entry的 python jail - [Nightmares](https://github.com/ctfs/write-ups/tree/master/plaid-ctf-2014/nightmares)

>只剩下3成的RSA key reconstruction - [RSA](https://github.com/ctfs/write-ups/tree/master/plaid-ctf-2014/rsa)


不過沒有那麼多時間可以寫write-up , 只能寫一篇,
挑一個解最久的題目zfs來寫XD

這題是跟atdog一起解的,前面卡關卡很久,
最後atdog用人眼讀zfs data structure把key看出來


........未完待續   


// 我承認我只是想先曬計分板(被毆飛







