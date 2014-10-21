Title: solr QueryConverter的定制
Date: 2013-12-02 20:53
Author: suhong
Category: 技术流
Slug: solr-queryconverter-custom

<div>
其实我这里没有定制QueryConverter，只是基于此解决了一个问题。我在solr中引入suggest功能，Solr默认的QueryConverter为SpellQueryConvert，它是根据空格对查询参数做分隔，导致
“nokia
e”这样的字符被当作“nokia”，“e”这样的两个字符处理，不符合要求。例如索引中有数据“nokia
email”，我需要“nokia
e”作为一个整体处理获取suggestions，而分成“nokia”，“e”两个字符处理得到的数据并不是想要的。  

方法一：找到SpellCheckComponent类加断点调试发现那里有个判断函数，会先取spellcheck.q的值，如果不为空，按其值处理获得token
stream处理，如果为空，则按q获取值处理得到tokens
stream，这里这两个处理并不一样：  
String q = params.get(SPELLCHECK\_Q);

</div>
<div>
if (q != null) {  
//we have a spell check param, tokenize it with the query analyzer
applicable for this spellchecker  
tokens = getTokens(q, spellChecker.getQueryAnalyzer());  
} else {  
q = rb.getQueryString();  
if (q == null) {  
q = params.get(CommonParams.Q);  
}  
tokens = queryConverter.convert(q);  
}

前者处理“nokia e”后得到的token还是“nokia
e”，而后者处理后得到的token为[nokia,e]，也就是说前者才是符合我们需求的，所以在solrj中suggest功能模块用spellcheck.q查询。

方法二：在solrconfig中添加

<queryConverter name="phraseQueryConverter"/\>

调试源码时发现还有一个SuggestQueryConverter类，与SpellQueryConvert一样继承QueryConverter类，查看官方WIKI解释发现

    queryConverter默认值是SpellQueryConvert，这里加入配置改成SuggestQueryConverter即可，无需再定制QueryConverter，当然你可以定制QueryConverter实现你所需要的需求。其实这里的SuggestQueryConverter中和方法一if中的处理是一样的。实现结果如下：

    <response>
      <lst name="responseHeader">
      <int name="status">0</int>
      <int name="QTime">0</int>
      </lst>
      <lst name="spellcheck">
        <lst name="suggestions">
          <lst name="motorola x">
            <int name="numFound">10</int>
            <int name="startOffset">0</int>
            <int name="endOffset">10</int>
            <arr name="suggestion">
              <str>motorola xoom 3g版</str>
              <str>motorola xt875</str>
              <str>motorola xt300</str>
              <str>motorola xt883</str>
              <str>motorola xt702</str>
              <str>motorola xt806</str>
              <str>motorola xt800</str>
              <str>motorola xt502</str>
              <str>motorola xt882</str>
              <str>motorola xt316</str>
            </arr>
          </lst>
          <str name="collation">motorola xoom 3g版</str>
      </lst>
    </lst>
    </response>        （这里引用的数据来自于http://www.colorfuldays.org/tag/solr/）

</div>

