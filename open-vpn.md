Title: Ubuntu中搭建Open-vpn服务器
Date: 2013-10-26 16:31
Author: neoyin
Category: 技术流
Tags: openvpn
Slug: open-vpn

上一次在Ubuntu中搭建了一个
[PPTP-VPN](http://www.floatinglife.cn/ubuntu-vpn "Ubuntu中快速搭建VPN服务器")
服务
今天决定更进一步，搭建一个openvpn服务。毕竟相对于PPTP来说OpenVPN加密型更强，而且穿透性更强。

So Let's do it.

**安装：**

    apt-get install openvpn
    将OpenVPN文件及配置拷贝到/etc/openvpn/目录下
    cp -r /usr/share/doc/openvpn/examples/easy-rsa/2.0 /etc/openvpn/
    gzip -d /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz
    cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf /etc/openvpn/

**配置:**

#### <!--more-->

#### 配置PKI

    cd /etc/openvpn/
    # vim vars

    这个自己根据情况改一下，不改也没有问题
    例如：
    export KEY_COUNTRY=”CN”
    export KEY_PROVINCE=”BJ”
    export KEY_CITY=”Beijing”
    export KEY_ORG=”floatinglife.cn”
    export KEY_EMAIL=”neo@fl.cn“
    ..
    注：在后面生成服务端ca证书时，这里的配置会作为缺省配置
    将SSL配置文件软链： 
    ln -s openssl-1.0.0.cnf openssl.cnf

#### 生成CA证书

    source ./vars
    #NOTE: If you run ./clean-all, I will be doing a rm -rf on /etc/openvpn/keys
    #清空原有证书
    ./clean-all
    #生成服务器端ca证书
    ./build-ca  #由于之前做过缺省配置，这里一路回车即可
    ./build-key-server floatinglife.cn ＃服务器名，也可以自定义，可以随便起
    ＃生成DH验证文件
    ./build-dh   ＃生成diffie hellman参数，用于增强openvpn安全性
    ＃生成客户端证书
    ./build-key client name
    #生成ta.key文件
    openvpn --genkey --secret /etc/openvpn/keys/ta.key

编辑服务配置文件

    vim /etc/openvpn/server.conf

    # 设置监听IP，默认是监听所有IP
    ;local a.b.c.d

    # 设置监听端口，必须要对应的在防火墙里面打开
    port 1194

    # 设置用TCP还是UDP协议？
    ;proto tcp
    proto tcp

    # 设置创建tun的路由IP通道，还是创建tap的以太网通道路由IP容易控制，所以推荐使用它；
    # 但如果如IPX等必须使用第二层才能通过的通讯，则可以用tap方式，tap也就是以太网桥接
    ;dev tap
    dev tun

    # 这里是重点，必须指定SSL/TLS root certificate (ca),
    # certificate(cert), and private key (key)
    # ca文件是服务端和客户端都必须使用的，但不需要ca.key
    # 服务端和客户端指定各自的.crt和.key
    # 请注意路径,可以使用以配置文件开始为根的相对路径,
    # 也可以使用绝对路径
    # 请小心存放.key密钥文件
    ca keys/ca.crt
    cert keys/floatinglife.crt
    key keys/floatinglife.key # This file should be kept secret

    # 指定Diffie hellman parameters.
    dh keys/dh1024.pem

    # 配置VPN使用的网段，OpenVPN会自动提供基于该网段的DHCP服务，但不能和任何一方的局域网段重复，保证唯一
    server 10.8.0.0 255.255.255.0

    # 维持一个客户端和virtual IP的对应表，以方便客户端重新连接可以获得同样的IP
    ifconfig-pool-persist ipp.txt

    # 为客户端创建对应的路由,以另其通达公司网内部服务器
    # 但记住，公司网内部服务器也需要有可用路由返回到客户端
    ;push "route 192.168.20.0 255.255.255.0"
    push "route 10.6.0.0 255.255.0.0"

    # 若客户端希望所有的流量都通过VPN传输,则可以使用该语句
    # 其会自动改变客户端的网关为VPN服务器,推荐关闭
    # 一旦设置，请小心服务端的DHCP设置问题
    ;push "redirect-gateway"

    # 用OpenVPN的DHCP功能为客户端提供指定的DNS、WINS等
    ;push "dhcp-option DNS 10.8.0.1"
    ;push "dhcp-option WINS 10.8.0.1"

    # 默认客户端之间是不能直接通讯的，除非把下面的语句注释掉
    client-to-client

    # 下面是一些对安全性增强的措施
    # For extra security beyond that provided by SSL/TLS, create an "HMAC firewall"
    # to help block DoS attacks and UDP port flooding.
    #
    # Generate with:
    # openvpn --genkey --secret ta.key
    #
    # The server and each client must have a copy of this key.
    # The second parameter should be 0 on the server and 1 on the clients.
    tls-auth ta.key 0 # This file is secret

    # 使用lzo压缩的通讯,服务端和客户端都必须配置
    comp-lzo

    # 输出短日志,每分钟刷新一次,以显示当前的客户端
    status /var/log/openvpn/openvpn-status.log

    # 缺省日志会记录在系统日志中，但也可以导向到其他地方
    # 建议调试的使用先不要设置,调试完成后再定义
    log         /var/log/openvpn/openvpn.log
    log-append  /var/log/openvpn/openvpn.log

    # 设置日志的级别
    #
    # 0 is silent, except for fatal errors
    # 4 is reasonable for general usage
    # 5 and 6 can help to debug connection problems
    # 9 is extremely verbose
    verb 3

启动服务 service openvpn start

<div>
设置外网访问

</div>
<div>
<div>
    # vim /etc/sysctl.conf

</div>
</div>
<div>
<div>
找到net.ipv4.ip\_forward = 0

</div>
<div>
把0改成1

</div>
</div>
<div>
<div>
    # sysctl -p

</div>
</div>
设置iptables（这一条至关重要，通过配置nat将vpn网段IP转发到server内网）

<div>
<div>
    # iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE

</div>
</div>
设置openvpn端口通过：

<div>
<div>
    # iptables -A INPUT -p TCP --dport 1194 -j ACCEPT
    # iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

</div>
</div>
<div>
<div>
**这边需要注意的是Debian/Ubuntu上iptables是不会保存规则的。**

</div>
<div>
需要按如下步骤进行配置，让网卡关闭时保存iptables规则，启动时加载iptables规则：

</div>
<div>
</div>
<div>
创建/etc/network/if-post-down.d/iptables 文件

</div>
</div>
<div>
<div>
    # vim /etc/network/if-post-down.d/iptables

</div>
</div>
添加如下内容：

<div>
    #!/bin/bash
    iptables-save > /etc/iptables.rules

</div>
<div>
<div>
保存并退出，然后添加执行权限。

</div>
</div>
<div>
<div>
    # chmod +x /etc/network/if-post-down.d/iptables

</div>
</div>
<div>
<div>
创建/etc/network/if-pre-up.d/iptables 文件

</div>
</div>
<div>
<div>
    # vim /etc/network/if-pre-up.d/iptables

</div>
</div>
添加如下内容：

<div>
    #!/bin/bash
    iptables-restore < /etc/iptables.rules

</div>
<div>
<div>
保存并退出，然后添加执行权限。

</div>
</div>
<div>
<div>
    # chmod +x /etc/network/if-pre-up.d/iptables

</div>
</div>

客户端配置
----------

**将服务器端生成的key（ca.crt，client.crt，client.key，ta.key）下载到本地。**

<div>
<div>
进入客户端OpenVPN目录，将sample-config下的client.ovpn文件复制到config目录，

</div>
<div>
client端做相应的修改：

</div>
</div>
<div>
    remote $server_ip 1194
    proto tcp
    dev tun
    ca ca.crt
    cert xxx.crt
    key xxx.key
    tls-auth ta.key 1
    comp-lzo
    user nobody
    group nobody
    persist-key
    persist-tun

</div>
<div id="mac">
### Mac客户端

<div>
<div>
[https://code.google.com/p/tunnelblick/](https://code.google.com/p/tunnelblick/)

</div>
<div>
1.打开Tunnelblick

</div>
<div>
2.点击左下角+

</div>
<div>
3.我有设置文件

</div>
<div>
4.OpenVPN设置

</div>
<div>
5.打开私人设置文件夹

</div>
<div>
6.将key和新建的client.ovpn放到此目录下

</div>
</div>
</div>
<div id="linux">
### Linux客户端

<div>
<div>
    # yum install openvpn
    # openvpn --daemon --config client.ovpn

</div>
</div>
</div>

