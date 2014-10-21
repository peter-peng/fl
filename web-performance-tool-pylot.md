Title: 压力测试工具-Pylot 
Date: 2013-05-06 16:22
Author: neoyin
Category: 技术流
Tags: Pylot, python, 压力测试
Slug: web-performance-tool-pylot

Pylot（[www.pylot.org](http://www.pylot.org/)）是一款开源的用以测试Web
Service性能和扩展性的工具，它运行HTTP负载测试，这对于制定容量计划、确定基准点、分析系统瓶颈以及系统调优都非常有用。在使用过程中，Pylot会发起并发请求（HTTP
Requests），检验服务器响应，以及带有相关指标的报表。它通过GUI或者Shell/Console来执行和监视对被测试网站的测试过程。

你开始在一个XML文件(testcases.xml)中定义你的测试用例。测试用例是验证你指定的请求（URL，Method，Body等）。可以验证服务器响应的正则表达式匹配的内容，HTTP状态码。您可以通过调整负载的工作量控制GUI或命令行设置，然后再开始测试运行（数目的代理，请求的时间间隔，上升时间，测试时间）。这些设置，您可以根据不同的负载情况下的模型试验。在运行加载的情况下，并传递到负载产生引擎。代理调度和运行，同时发送HTTP请求到你的web服务。它在执行过程中生成实时的统计和错误报告，通过这个报告可以很方便的进行监控。

![pylot\_03](http://www.floatinglife.cn/wp-content/uploads/2013/05/pylot_03.png)
--------------------------------------------------------------------------------

安装起来挺简单基于python 2.5+ 也需要安装NumPy和Matplotli
以便生成图形界面.

<!--more-->

看这里:[http://pylot.org/gettingstarted.html](http://pylot.org/gettingstarted.html)

产品特点
--------

-   HTTP和HTTPS（SSL）支持
-   多线程负载生成器
-   自动cookie处理
-   响应验证与正则表达式
-   执行/监控控制台
-   实时统计
-   结果报告和图形
-   定制的timer
-   GUI模式
-   外壳/控制台模式
-   跨平台

配置testcase

    <testcase>
    <param name="sneak_project" value="http://sneak.l99.com/" />
    <case>
      <url>http://www.l99.com/EditAccount_login.action</url>
      <method>POST</method>
      <body><![CDATA[e=15101&m=951626]]></body>
      <add_header>Content-Type: application/x-www-form-urlencoded</add_header>

      <case><url>${sneak_project}myGameFriend.action</url></case>
      <case><url>${sneak_project}showGame.action</url></case>
      <case><url>${sneak_project}shop.action</url></case>
      <case><url>${sneak_project}buyRes.action</url><method>POST</method><body><![CDATA[resId=45&resNum=1]]></body></case>
    </case>
    </testcase>

#### 配置选项：

“${pylot\_path}/core/config.py的包含了一些全局配置选项。您可以设置某些defauls和改变某些行为。如果在命令行上指定这些选项是覆盖。

    AGENTS = 1
    DURATION = 60  # secs
    RAMPUP = 0  # secs
    INTERVAL = 0  # millisecs
    TC_XML_FILENAME = 'testcases.xml'
    OUTPUT_DIR = None
    TEST_NAME = None
    LOG_MSGS = False

    GENERATE_RESULTS = True
    SHUFFLE_TESTCASES = False  # randomize order of testcases per agent
    WAITFOR_AGENT_FINISH = True  # wait for last requests to complete before stopping
    SMOOTH_TP_GRAPH = 1  # secs.  smooth/dampen throughput graph based on an interval
    SOCKET_TIMEOUT = 300  # secs
    COOKIES_ENABLED = True

    HTTP_DEBUG = False  # only useful when combined with blocking mode  
    BLOCKING = False  # stdout blocked until test finishes, then result is returned as XML
    GUI = False

    -a, --agents=NUM_AGENTS设置同时访问用户数量
    -d, --duration=DURATION设置总测试时间（秒）
    -r, --rampup=RAMPUP设置提升量（秒），没太懂是什么意思
    -i, --interval=INTERVAL设置访问间隔（毫秒）
    -x, --xmlfile=TEST_CASE_XML设置要使用的xml文件，默认testcase.xml
    -o, --output_dir=PATH设置输出文件路径
    -n, --name=TESTNAME设置测试名称
    -l, --log_msgs设置是否需要日志信息
    -b, --blocking设置是否开启锁定模式，如果开启会锁定输出直到测试结束
    -g, --gui设置是否使用图形界面
    -p, --port=PORT设置xml-rpc监听的端口

    #启动测试...
    python pylot -a 50

测试结果
--------

![o\_4](http://www.floatinglife.cn/wp-content/uploads/2013/05/o_4.jpg)

Workingload Model里分别是测试总时间、用户数以及提升和间隔时间。

Results
Summary里是测试结果统计，可以看到总请求数（requests）、总错误数（errors）和总接受数据量（data
received）。

avg是平均访问时间，stdev是标准差，min是最小值，max是最大值。50th %表示
排在50%位置（也就是中间位置）的访问时间，其他以此类推。

官方示例:[http://www.pylot.org/samples/results/results.html](http://www.pylot.org/samples/results/results.html)

参考：

-   [http://pylot.org/](http://pylot.org/)
-   [http://www.cnblogs.com/limengqiang/archive/2013/02/07/Pylot.html](http://www.cnblogs.com/limengqiang/archive/2013/02/07/Pylot.html)
-   [http://www.cnblogs.com/numbbbbb/archive/2013/04/19/3029959.html](http://www.cnblogs.com/numbbbbb/archive/2013/04/19/3029959.html)


