Title: CURL获取请求的各响应时间
Date: 2014-04-23 04:07
Author: neoyin
Category: 技术流
Tags: curl
Slug: curl-request-response-time

```
curl -o /dev/null  -w %{time_namelookup}::%{time_connect}::%{time_starttransfer}::%{time_total}::%{speed_download}"\n" "http://dbapi.l99.com"
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
104   208  104   208    0     0     41      0  0:00:05  0:00:05 --:--:--  101k
5.007::5.007::5.010::5.010::41.000
```
-   -o：把curl 返回的数据写到垃圾回收站[ /dev/null]
-   -s：去掉所有状态
-   -w：按照之后的格式写出

-   time\_namelookup：DNS 解析域名的时间

-   time\_commect：client和server端建立TCP 连接的时间
    **里面包括DNS解析的时间**
-   time\_starttransfer：从client发出请求；到web的server
    响应第一个字节的时间 **包括前面的2个时间**
-   time\_total：client发出请求；到web的server发送会所有的相应数据的时间
-   speed\_download：下周速度 单位 byte/s

<!--more-->

-w, --write-out  

以下变量会按CURL认为合适的格式输出，输出变量需要按照%{variable\_name}的格式，如果需要输出%，double一下即可，即%%，同时，\\n是换行，\\r是回车，\\t是TAB。

**url\_effective** The URL that was fetched last. This is most
meaningful if you've told curl to follow location: headers.

**filename\_effective** The ultimate filename that curl writes out to.
This is only meaningful if curl is told to write to a file with the
--remote-name or --output option. It's most useful in combination with
the --remote-header-name option. (Added in 7.25.1)

**http\_code**
http状态码，如200成功,301转向,404未找到,500服务器错误等。(The numerical
response code that was found in the last retrieved HTTP(S) or FTP(s)
transfer. In 7.18.2 the alias response\_code was added to show the same
info.)

**http\_connect** The numerical code that was found in the last response
(from a proxy) to a curl CONNECT request. (Added in 7.12.4)

**time\_total** 总时间，按秒计。精确到小数点后三位。 （The total time, in
seconds, that the full operation lasted. The time will be displayed with
millisecond resolution.）

**time\_namelookup** DNS解析时间,从请求开始到DNS解析完毕所用时间。(The
time, in seconds, it took from the start until the name resolving was
completed.)

**time\_connect**
连接时间,从开始到建立TCP连接完成所用时间,包括前边DNS解析时间，如果需要单纯的得到连接时间，用这个time\_connect时间减去前边time\_namelookup时间。以下同理，不再赘述。(The
time, in seconds, it took from the start until the TCP connect to the
remote host (or proxy) was completed.)

**time\_appconnect**
连接建立完成时间，如SSL/SSH等建立连接或者完成三次握手时间。(The time, in
seconds, it took from the start until the SSL/SSH/etc connect/handshake
to the remote host was completed. (Added in 7.19.0))

**time\_pretransfer** 从开始到准备传输的时间。(The time, in seconds, it
took from the start until the file transfer was just about to begin.
This includes all pre-transfer commands and negotiations that are
specific to the particular protocol(s) involved.)

**time\_redirect**
重定向时间，包括到最后一次传输前的几次重定向的DNS解析，连接，预传输，传输时间。(The
time, in seconds, it took for all redirection steps include name lookup,
connect, pretransfer and transfer before the final transaction was
started. time\_redirect shows the complete execution time for multiple
redirections. (Added in 7.12.3))

**time\_starttransfer** 开始传输时间。在发出请求之后，Web
服务器返回数据的第一个字节所用的时间(The time, in seconds, it took from
the start until the first byte was just about to be transferred. This
includes time\_pretransfer and also the time the server needed to
calculate the result.)

**size\_download** 下载大小。(The total amount of bytes that were
downloaded.)

**size\_upload** 上传大小。(The total amount of bytes that were
uploaded.)

**size\_header** 下载的header的大小(The total amount of bytes of the
downloaded headers.)

**size\_request** 请求的大小。(The total amount of bytes that were sent
in the HTTP request.)

**speed\_download** 下载速度，单位-字节每秒。(The average download speed
that curl measured for the complete download. Bytes per second.)

**speed\_upload** 上传速度,单位-字节每秒。(The average upload speed that
curl measured for the complete upload. Bytes per second.)

**content\_type**
就是content-Type，不用多说了，这是一个访问我博客首页返回的结果示例(text/html;
charset=UTF-8)；(The Content-Type of the requested document, if there was
any.)

**num\_connects** Number of new connects made in the recent transfer.
(Added in 7.12.3)

**num\_redirects** Number of redirects that were followed in the
request. (Added in 7.12.3)

**redirect\_url** When a HTTP request was made without -L to follow
redirects, this variable will show the actual URL a **redirect** would
take you to. (Added in 7.18.2)

**ftp\_entry\_path** The initial path libcurl ended up in when logging
on to the remote FTP server. (Added in 7.15.4)

**ssl\_verify\_result** ssl认证结果，返回0表示认证成功。( The result of
the SSL peer certificate verification that was requested. 0 means the
verification was successful. (Added in 7.19.0))

若多次使用-w参数，按最后一个的格式输出。If this option is used several
times, the last one will be used.

[http://digdeeply.org/archives/05102012.html](http://digdeeply.org/archives/05102012.html)
