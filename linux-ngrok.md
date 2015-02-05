Title: ngrok-神器让你外网访问本地
Date: 2015-02-04 13:59
Author: Neo
Category: 技术流
Tags: linux
Slug: linux-ngrok

> ngrok 是一个反向代理，通过在公共的端点和本地运行的 Web 服务器之间建立一个安全的通道。ngrok 可捕获和分析所有通道上的流量，便于后期分析和重放

神器，ngrok绝对是一件神器
来看一下他的使用帮助

```
grok -h
Usage: ngrok [OPTIONS] <local port or address>
Options:
  -authtoken="": Authentication token for identifying an ngrok.com account
  -config="": Path to ngrok configuration file. (default: $HOME/.ngrok)
  -hostname="": Request a custom hostname from the ngrok server. (HTTP only) (requires CNAME of your DNS)
  -httpauth="": username:password HTTP basic auth creds protecting the public tunnel endpoint
  -log="none": Write log messages to this file. 'stdout' and 'none' have special meanings
  -proto="http+https": The protocol of the traffic over the tunnel {'http', 'https', 'tcp'} (default: 'http+https')
  -subdomain="": Request a custom subdomain from the ngrok server. (HTTP only)

Examples:
	ngrok 80
	ngrok -subdomain=example 8080
	ngrok -proto=tcp 22
	ngrok -hostname="example.com" -httpauth="user:password" 10.0.0.1


Advanced usage: ngrok [OPTIONS] <command> [command args] [...]
Commands:
	ngrok start [tunnel] [...]    Start tunnels by name from config file
	ngrok help                    Print help
	ngrok version                 Print ngrok version

Examples:
	ngrok start www api blog pubsub
	ngrok -log=stdout -config=ngrok.yml start ssh
	ngrok version
```

执行`ngrok -proto=tcp 22`进行tcp端口转发就可以在外网ssh到本机了
```
Tunnel Status                 online
Version                       1.7/1.7
Forwarding                    tcp://ngrok.com:45629 -> 127.0.0.1:22
Web Interface                 127.0.0.1:4040
# Conn                        0
Avg Conn Time                 0.00ms
```
ngrok利用随机端口进行转发.
你可以用`ssh username@ngrok.com -p 45629`可以外网登陆了

可以采用自定义二级域名test.ngrok.com转发本机的8080端口。
```
ngrok -subdomain test 8080
```
哦,忘了ngrok的官网[https://ngrok.com/](https://ngrok.com/) 去试试吧.

---

- [https://ngrok.com/](https://ngrok.com/)
- [http://www.yaosansi.com/post/ngrok-secure-introspectable-tunnels-to-localhost/](http://www.yaosansi.com/post/ngrok-secure-introspectable-tunnels-to-localhost/)
- [http://dorole.com/tag/ngrok/](http://dorole.com/tag/ngrok/)




