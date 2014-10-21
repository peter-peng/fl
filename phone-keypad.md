Title: 【What if 系列】恼人的键盘
Date: 2013-12-19 15:00
Author: neoyin
Category: 【What if系列】
Tags: what if
Slug: phone-keypad

提问：
-----

> 在一些老式的手机上输入一个英语字母时我需要按很多次同样的数字键，比如要打出“Y”我需要按3次数字9。一些单词有连续好几个字母都在同一个数字键上，因而每打完一个字母你都要等一小会儿才能输入下一个字母，这让打字变成一件恼人的事情。所以我想问的是有哪个英语单词有连续最多的字母在同一个数字键上？
>
> —— Stewart Bishop

回答：
-----

我们可以用下面这段让人头疼的Shell指令来回答你这个问题，这段代码能在给定的单词列表中找出所有有连续字母在同一个数字键上的单词：

    cat wordlist.txt | perl -pe 's/^(.*)$/L$&U$&/g' | 'ABCDEFGHIJKLMNOPQRSTUVWXYZ' '2223334445556667777888999' | grep -P "(.)11111"

根据这段代码跑出来的结果，**nonmonogamous**是最让人头疼的单词，因为它有连续七个字母（**nonmono**）在数字键6上。[1]

<!--more-->

**九宫格键盘**

很少有单词所有的字母都在一个按键上；常见的单词中最长的也就几个字母。[2]不过如果只用这些单词的话，我们可以写出像“电视上的高清MMO游戏（a
high def MMO on TV）”这样的每个单词所有字母都在一个按键上的短语。

![image](http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=fdb5ff5f0b24ab18e416e13205c197f0/d1160924ab18972b7bd41ab3e4cd7b899f510ad8.jpg?referer=e543d94a718da9771738b21b76b7&x=.jpg)

（再让我看一个小时）

当然了这种短语还有许多，虽然其中一些需要一些想象力：

![image](http://g.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=f55d3505530fd9f9a417556c1516a517/1b4c510fd9f9d72a7efc0a64d62a2834349bbb2a.jpg?referer=d54a59404936acaf00f7a2cc05e8&x=.jpg)

这种恼人的输入困难并不局限于九宫格键盘上。对于任何输入系统，你总能找到很难输入的短语。

**QWERTY键盘**

单词极客们都知道在QWERTY键盘上只有左手可以打出的最长的单词是“stewardesses”。

事实上，只用左手打出完整的句子也是可以做到的。比如这句话：我们在一个秘密星际争霸聚会预定了座位（**We
reserved seats at a secret Starcraft fest**）。很神奇，是不是？

如果你施加不同的限制条件，在一些看上去更加脏乱差的shell指令和Python代码[3]的帮助下你还能找出更多的例子：

**只用左手**

![image](http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=2acf0992b4fd5266a32b3c119b23e616/38dbb6fd5266d016a2209b51952bd40735fa3589.jpg?referer=b513bacbbb014a90d829738d7388&x=.jpg)

（值了）

![image](http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=775bfb768594a4c20e23e72e3ecf6ae8/d53f8794a4c27d1e73c821bd19d5ad6edcc438d8.jpg?referer=d923d87fadc37931247fb21978b7&x=.jpg)

（与在你附近的单身雪貂会面吧）

**只用右手**

![image](http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=5e1decb4fe039245a5b5e10ab7afd5ff/c2fdfc039245d68849eca8dda6c27d1ed31b24d8.jpg?referer=d628d4a4a964034f56daf63684b6&x=.jpg)

（你何来的勇气？）

![image](http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=e5ba91406963f624185d3906b77f9ac5/bf096b63f6246b601b96a80de9f81a4c510fa22a.jpg?referer=b6e1827d5b82b2b7fe880df40ee8&x=.jpg)

**只用中间一行键盘**

![image](http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=2d72e258708b4710ca2ffdc9f3f5b2c0/cc11728b4710b9125f2fec87c1fdfc03934522d8.jpg?referer=3a98fe5a71f082027485a50f8eb6&x=.jpg)

（他看了一眼硬盘里的内容，立刻就挂了）

![image](http://c.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=2316f8b5c1cec3fd8f3ea770e6b3a502/c9fcc3cec3fdfc030841e6c1d63f8794a4c22689.jpg?referer=c0ab944649fbfbed854e034f8287&x=.jpg)

（但是她还是很不错！）

![image](http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=80f7b76b700e0cf3a4f74efe3a7d8322/9922720e0cf3d7ca6b6e250ff01fbe096b63a92a.jpg?referer=83b69f37c880653822fd902317e8&x=.jpg)

（其实是薯片）

**只用最上面一行键盘**

![image](http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=0610bf1b9a22720e7fcee2ff4bf07b7b/91529822720e0cf3d82d54820846f21fbf09aaf4.jpg?referer=bdf6bc5ab27eca804b120dd716aa&x=.jpg)

（我们还带了牛奶！）

![image](http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=2b82f95e7d3e6709ba0045fa0bfcee00/c8177f3e6709c93dfccb0b8e9d3df8dcd1005489.jpg?referer=b9fed68479899e5121990f249487&x=.jpg)

（悲剧之处在于诗集其实挺不错的）

最后，如果有人问你为什么在社交媒体上不更活跃点，你可以很酷地告诉他们你“人见人爱花见花开（too
pretty to tweet，只用了最上面一行键盘）”

![image](http://g.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=f13a5740cb3d70cf48faaa08c8e7a03d/6609c93d70cf3bc71139c895d300baa1cd112a89.jpg?referer=f58dae180db30f246c8dd9339687&x=.jpg)

* * * * *

-   注1：当然了如果你选取一些生僻词的话可能会有比这更长的满足条件的单词，只不过我用的单词表里没有而已。
-   注2：比如“tutu”这个单词
-   注3：我是用程序在文本日志里找出满足一定条件的文字片段，然后用“马尔可夫链”方法把它们随机连起来。我的代码在[这里](http://xkcd.com/markov.py.txt)可以看到。

-   原文来源：[what-if.xkcd.com](http://what-if.xkcd.com/75/)
-   原文标题：[Phone
    Keypad](http://source.yeeyan.org/view/501282_33a "Phone Keypad")
-   原文地址：[http://what-if.xkcd.com/75/](http://what-if.xkcd.com/75/)

