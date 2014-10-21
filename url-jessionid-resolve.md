Title: 解决URL中的jessionid
Date: 2014-02-26 10:36
Author: neoyin
Category: 技术流
Tags: jessionid, tomcat
Slug: url-jessionid-resolve

<div style="display: none;">
[sac longchamp
femme](http://burlsworthtrophy.com/wp-wpp.php?p=sac-longchamp-femme)

</div>
</p>
最近在网站上加了通过[二维码](http://www.floatinglife.cn/qrcode-generate)分享微信功能,但是发现用户通过二维码分享页面的时候时不时链接中会带上jessionid=xxxxxx,这样的URL极不友好，对搜索引擎不利，而且也会带来安全隐患。

jessionid是什么可以看这里[jsessionid释疑解惑](http://www.blogjava.net/zhaozhenlin1224/archive/2010/02/03/311807.html)

产生的原因
----------

当第一次访问服务器的时候，服务端并不知道你的客户端浏览器是否支持cookie，因此，第一次请求发起的时候，服务端会默认url重写，也就是将sessionid写到url中传递;在请求过后，服务器会根据你提交的客户端浏览器信息自动检查客户端是否启用了cookie，如果启用，将不再进行url重写。如果没有，则还是进行url重写

解决方法
--------

1.  通过加入 Filter 的方式过滤掉 URL 中包含的 jsessionid，再重新包装
    Response

<!--more-->

    import javax.servlet.*;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import javax.servlet.http.HttpServletResponseWrapper;
    import javax.servlet.http.HttpSession;
    import java.io.IOException;

    /**
     * Servlet filter which disables URL-encoded session identifiers.
     * 
    * 

     * Copyright (c) 2006, Craig Condit. All rights reserved.
     *
    * Redistribution and use in source and binary forms, with or without
     * modification, are permitted provided that the following conditions are met:
     * 
    *   * Redistributions of source code must retain the above copyright notice,
     *     this list of conditions and the following disclaimer.
     *   * Redistributions in binary form must reproduce the above copyright notice,
     *     this list of conditions and the following disclaimer in the documentation
     *     and/or other materials provided with the distribution.
     * 

    * THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
     * AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
     * IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
     * ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE
     * LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
     * CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
     * SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
     * INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
     * CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
     * ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
     * POSSIBILITY OF SUCH DAMAGE.
     * 
     */
    @SuppressWarnings("deprecation")
    public class DisableUrlSessionFilter implements Filter {

        /**
         * Filters requests to disable URL-based session identifiers.
         */
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
            // skip non-http requests
            if (!(request instanceof HttpServletRequest)) {
                chain.doFilter(request, response);
                return;
            }

            HttpServletRequest httpRequest = (HttpServletRequest) request;
            HttpServletResponse httpResponse = (HttpServletResponse) response;

            // clear session if session id in URL
            if (httpRequest.isRequestedSessionIdFromURL()) {
                HttpSession session = httpRequest.getSession();
                if (session != null) session.invalidate();
            }

            // wrap response to remove URL encoding
            HttpServletResponseWrapper wrappedResponse = new HttpServletResponseWrapper(httpResponse) {
                @Override
                public String encodeRedirectUrl(String url) {
                    return url;
                }

                @Override
                public String encodeRedirectURL(String url) {
                    return url;
                }

                @Override
                public String encodeUrl(String url) {
                    return url;
                }

                @Override
                public String encodeURL(String url) {
                    return url;
                }
            };

            // process next request in chain
            chain.doFilter(request, wrappedResponse);
        }

        /**
         * Unused.
         */
        public void init(FilterConfig config) throws ServletException {
        }

        /**
         * Unused.
         */
        public void destroy() {
        }

2.tomcat中配置(version在6.0.30以上)

    <?xml version='1.0' encoding='utf-8'?>
    <Context docBase="PATH_TO_WEBAPP" path="/CONTEXT" disableURLRewriting="true">
    </Context>

3.如果引入了[rewrite
filter](http://urlrewritefilter.googlecode.com/svn/trunk/src/doc/manual/3.2/guide.html)

    <outbound-rule encodefirst="true">
      <name>Strip URL Session ID's</name>
      <from>^(.*?)(?:\;jsessionid=[^\?#]*)?(\?[^#]*)?(#.*)?$</from>
      <to>$1$2$3</to>
    </outbound-rule>

4.Tomcat7中web.xml中配置

    <session-config>
        <tracking-mode>COOKIE</tracking-mode>
    </session-config>

参考:
-----

[https://randomcoder.org/articles/jsessionid-considered-harmful](https://randomcoder.org/articles/jsessionid-considered-harmful)

[http://www.oschina.net/question/12\_19181](http://www.oschina.net/question/12_19181)

[http://www.blogjava.net/freeman1984/archive/2010/09/09/331501.html](http://www.blogjava.net/freeman1984/archive/2010/09/09/331501.html)

[URL中允许携带sessionid带来的安全隐患](http://www.cnblogs.com/BearsTaR/archive/2010/08/24/URL_SESSION_ID_LEEK.html)
