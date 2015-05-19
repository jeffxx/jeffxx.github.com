---
layout: post
title: "Defcon 22 Qual Sick Writeup"
date: 2014-05-19 19:24:00 +0800
comments: true
categories: [defcon, ctf, misc]
---

Defcon CTF Qual 一直都是一年中競爭最激烈的比賽. 全世界一千多個隊伍搶12個決賽名額,
搶到門票的12隊加上8個種子隊(去年Defcon final冠軍, 七個世界各地CTF冠軍隊伍)
會在 8/7~ 8/10 於拉斯維加斯進行更刺激的 Attack & Defense 決賽.
***

這次與217,Chroot和lab成員奮鬥48小時,在剩下六小時的時候掉出20名外,

還好在最後一小時解出四分題,驚險奪得Defcon決賽門票 

{% img /images/writeup/defcon22_scoreboard.PNG %}


這題靠整個團隊的合作在一小時內快速解出, 實在太值得紀念. 故本次選擇寫這題的writeup

<!-- more -->

* **Category:** HJ 
* **Points:** 4 
* **Description:** 

> I am sorry Sick Yoda.
> [services.2014.shallweplayaga.me/comeandgetme.enc_99509db93ee7ef2ee463df934c2c027a](comeandgetme.enc_99509db93ee7ef2ee463df934c2c027a)
> [services.2014.shallweplayaga.me/pub.pem_c5ea59f35d1b7ebf6e9f140144c2a2b8](pub.pem_c5ea59f35d1b7ebf6e9f140144c2a2b8)

題目一開始給了一個RSA public key和一個加密檔

```
-----BEGIN PUBLIC KEY-----
MEEwDQYJKoZIhvcNAQEBBQADMAAwLQImC1+CPNtJhcI2UiY7iabqbVRDkz0le0uq
dgc87QWlj7qE+QS9LTcCAwEAAQ==
-----END PUBLIC KEY-----
```

可以看到RSA KEY只有308 bits, 貌似可以很快爆出p,q

剛好在某次CTF解過類似的題目,立刻把當時參考的資料拉出看

>http://m0x39.blogspot.tw/2012/12/0x00-introduction-this-post-is-going-to.html

atdog用pkcs1-conf拉出 n, 送到網站上解不出p,q

`n=1447960949958180889766108699794255593190305412016955260539401755003594509743125280111537463`

此時所有人各自找library/程式嘗試暴力破出p,q

最後Shik 使用 [Yafu](http://yafu.sourceforge.net/) 在 328.0232 秒後破出了 p , q

`p=1155392895670976232909180918778240881689447939`
`q=1253219537166446194660024618758541358223427517`

丟進rsatool 找出d

```
rsatool.py -e 65537 \
           -p 1155392895670976232909180918778240881689447939 \
           -q 1253219537166446194660024618758541358223427517 \
           -n 1447960949958180889766108699794255593190305412016955260539401755003594509743125280111537463 
```
`d=102ddee9144c93aca5fa57c97b19a985beca22a2c3b7ede3db15fec4c24969f337cca2bf069`

得到d之後就可以直接解密comeandgetme.enc

```
#cat comeandgetme.enc_99509db93ee7ef2ee463df934c2c027a \
        | rsacrack.py -d 102ddee9144c93aca5fa57c97b19a985beca22a2c3b7ede3db15fec4c24969f337cca2bf069 \
        b5f823cdb4985c23652263b89a6ea6d5443933d257b4baa76073ced05a58fba84f904bd2d37 \
        | strings
http://bit.ly/1jANwVx
```

從網址抓到一個mp3檔,所有人都聽了口音很重的英文15分鐘,發現最後一段有連續的雜訊

用goldwave分析雜訊的部分

{% img /images/writeup/defcon22_sick_noise.PNG %}
{% img /images/writeup/defcon22_sick_noise_zoomin.PNG %}

頻譜看起來有點奇怪,猜測是要將粗的看成0,細的看成1 

decode的方法有機會是moorse或是ascii binary

在這邊分頭進行解碼, moorse decode失敗
    
當成ascii binary時decode前面10幾個bytes都是printable字元, 

但只看前幾bytes看不出是什麼encode

遠在北京的 Shik 不曉得用什麼神奇的方法快速解出所有bytes
``
7rKM^ATVEq+D>k=E-"&n06:]FG%kGtF"T4%0f1M(BOP^nG@b`0@<l9o@4lD.04o$/B4!SU@:j+-AM@XR2e"R&3A<-q2E#,&2)[EQAS,O_AnE/oDIal'FEDJC3\N.-ATDs.@qB^61,(F>/ot]7Ci"<,E,8s.@:s"$D.PA3D.R'01LsuL2E*EJ3&3KN0K1gK1b^jsA2lJ%3FOa!0On
``

這串字困擾了我們許久, 嘗試了各種常見的encode:  caser, xor , uuencode ..... 都失敗

最後還是shik發現是ascii85 encode, decode解出兩個url

`http://services.2014.shallweplayaga.me/flag_76aff3e0d77b38900a66e35687effaff`
`http://services.2014.shallweplayaga.me/eme_23dc0673382940749407ad8619a41`

第一個連結是被加密過的flag, 第二個連結看起來少了3bytes, Orange寫了隻script爆出最後3byte

`http://services.2014.shallweplayaga.me/eme_23dc0673382940749407ad8619a410cc`

抓下來是隻elf x64, 只有encrypt功能

所有人分頭reverse binary和直接執行觀察encrypt的方式

最後得出結論是block encrypt, 4bytes 結果不互相影響

Orange用"The flag is:" encrypt與flag 比較 確定前八碼相同

然後討論如何暴力跑出flag,

由 Sean 提出了非常快速的方法, 先將所有可能的4字元組合產生出字典檔

再將字典檔用給的程式加密 比對是否有與flag相符的4bytes , 再從字典檔拉出相同位置的明文

這邊大家各自寫程式gen, 我偷懶的用maskprocessor , 
maskprocessor產生組合字串很快而且很好設定規則
```
#maskprocessor -o /tmp/dict --increment-min=4 --increment-max=4 -1 ?l?u?d?s ?1?1?1?1
```

但剛開始學python寫太慢, 最後是orange的script爆出flag.




```python
ok
0 ['fa\xb2\xc0'] The
4 ['K\x16\xf4\x11'] flag
8 ['\n-2F'] is: 
12 ['\xd9\xcb\x8d\x9d'] I h
16 ['k\x88rP'] ope
20 ['\xe4\x83w\r'] that
24 ['\xe5\xb1\xd8\x14'] did
28 ['\xc22H\x02'] n't
32 ['T\xa1\xa4\x92'] suck
36 ['abW\x95'] too
40 ['5\x94\x13\x92'] muc
44 ['[\x0f\x84\xaa'] 44 keyerror
```

`The flag is: I hope that didn't suck too muc`

最後4bytes因為換行字元與null bytes不在orange的字典清單裡所以match不到

Orange直接猜 `I hope that didn't suck too much.` 4分GET

// 所有人一起解出關鍵題目的感覺真棒! 紀念一下(L)





