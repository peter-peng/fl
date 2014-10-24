Title: 使用正则表达式匹配嵌套Html标签
Date: 2014-02-08 12:03
Author: chengz
Category: 技术流
Tags: regex
Slug: regex-html-tags


看起来很牛X的样子, 可惜看不懂
[使用正则表达式匹配嵌套Html标签](http://blog.sina.com.cn/s/blog_6cfb94df01019pa3.html)

概述
----

正则表达式是做文本解析工作必不可少的技能。如Web服务器日志分析，网页前端开发等。很多高级文本编辑器都支持正则表达式的一个子集，熟练掌握正则表达式，经常能够使你的一些工作事半功倍。例如统计代码行数，只需一个正则就搞定。嵌套Html标签的匹配是正则表达式应用中一个比较难的话题，因为它涉及到的正则语法比较多，也比较难。因此也就更有研究的价值。

思路
----

任何复杂的正则表达式都是由简单的子表达式组成的，要想写出复杂的正则来，一方面需要有化繁为简的功底，另外一方面，我们需要从正则引擎的角度去思考问题。关于正则引擎的原理，推荐《Mastering
Regular Expression》中文名叫《精通正则表达式》。挺不错的一本书。

OK，先确定我们要解决的问题——从一段Html文本中找出特定id的标签的innerHTML。

这里面最大的难点就是，Html标签是支持嵌套的，怎么能够找到指定标签相对应的闭合标签呢？

<!--more-->

我们可以这样想，先匹配最前面的起始标签，假设是div吧（<div），然后一旦遇到嵌套div，就“压入堆栈”，然后一遇到div结束标签了，就“弹出堆栈”。如果遇到结束标签的时候，堆栈里面已经没有东西了，那么匹配结束，此结束标签为正确的闭合标签。

我之所以能够这样去思考，是因为我了解过正则的特性，我知道正则中的平衡组能够实现我刚才说的“堆栈”操作。所以，如果我们要编写复杂正则表达式，需要对正则的一些高级特性至少有所了解，这样我们思考问题才有个方向。

实现
----

这里假设我们要匹配的文本是一段合法的Html文本。下面这段Html代码是从我的博客上拷贝下来的，作为我们的测试文本。我们要匹配的就是footer这个div的innerHTML，同时把标签名也捕获下来。

        <div style="background-color:gray;" id="footer">    
    <a id="gotop" href="#" onclick="MGJS.goTop();return false;">Top</a>    
    <a id="powered" href="http://wordpress.org/">WordPress</a>    <div id="copyright">        
    Copyright &copy; 2009 简单生活 —— Kevin Yang的博客    </div>    <div id="themeinfo">      
      Theme by <a href="http://www.neoease.com/">mg12</a>. Valid 
    <a href="http://validator.w3.org/check?uri=referer">XHTML 1.1</a>       
     and <a href="http://jigsaw.w3.org/css-validator/">CSS 3</a>.    </div></div>

这里我们需要借助Expresso工具来构建和测试编写的正则表达式。

匹配起始标签
------------

起始标签特征很好提取，以尖括号打头，然后跟着一连串英文字母，然后一大串属性中（非尖括号字符）匹配id（不区分大小写）=footer。需要注意的是，footer可以被双引号或者单引号包裹，也可以什么都不加。正则如下：

    <(?<HtmlTag>[\w]+)[^>]*\s[iI][dD]=(?<Quote>["']?)footer(?(Quote)\k<Quote>)["']?[^>]*>

上面的正则表达式需要做几点说明：

1.  <尖括号在正则中算是一个特殊字符，在显式捕获分组中用它将分组名括起来。但是因为开头的尖括号在此上下文下并不会出现解析歧义，因此加不加转义符效果是一样的。

2.  (?<groupname>RegEx)格式定义一个命名分组，我们在上面定义了一个HtmlTag的标签分组，用来存放匹配到的Html标签名。Quote分组是用来给后面的匹配使用的。

3.  (?(GroupName)Then|Else)是条件语句，表示当捕获到GroupName分组时执行Then匹配，否则执行Else匹配。上面的正则中，我们先尝试匹配footer字符串左边的引号，并将其存入LeftQuote分组中，然后在footer右侧进行条件解析，如果之前匹配到
    LeftQuote分组，那么右侧也应该批评LeftQuote分组。这样一来，我们就能精确匹配id的各种情况了。

匹配闭合标签
------------

    ((?<Nested><\k<HtmlTag>[^>]*>)|</\k<HtmlTag>>(?<-Nested>)|.*?)*</\k<HtmlTag>>

在成功匹配到起始标签之后，后面的Html文本可以分为三种情况：

A. 匹配到嵌套div起始标签<div，这个时候，需要将其捕获到Nested分组。

B. 匹配到嵌套div起始标签的闭合标签，这个时候，需要将之前的Nested分组释放

C.
其他任意文本。注意，需要使用.\*?方式关闭贪婪匹配，否则最后的闭合标签可能会过度匹配

使用(RegEx1|RegEx2|RegEx3)\*这种方式，可以将几个条件以或的形式组合起来，然后再取若干次匹配结果，最终再匹配闭合标签。其中(?<-Nested\>)是表示释放之前捕获的Nested分组。确切的语法是(?<n-m>)即使用N分组替换掉M分组，如果N分组没有指定或不存在，则释放M分组。

update：前面过于侧重分析了，最后没有给出一个完整的正则真是抱歉。

    <(?<HtmlTag>[\w]+)[^>]*\s[iI][dD]=(?<Quote>["']?)footer(?(Quote)\k<Quote>)["']?[^>]*>((?<Nested><\k<HtmlTag>[^>]*>)|</\k<HtmlTag>>(?<-Nested>)|.*?)*</\k<HtmlTag>>

上面这个正则能够匹配任意id=footer的html标签。

需要注意，此正则表达式需要设置SingleLine=true，这样点号才可以把换行符也匹配进去。

对于domoxz
的问题，如果要匹配p标签，那么只需将上述的正则中的HtmlTag替换成p即可。
文章来自 : 使用正则表达式匹配嵌套Html标签
