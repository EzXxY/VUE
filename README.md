# 1. 为什么要写这么一个项目？🤔

&emsp;&emsp;网上可以找到的热门的批量查询 IP 的工具有几点不能满足我的需求：

> ① 不支持查询 IPv6 地址；

> ② 查出的 IPv4 所在地区也不太准确；

> ③ 查出的 IP 没有厂商名称及 ASN 信息；

> ④ 给的在线表格结果直接复制某列的 IP 也很不方便；

> ⑤ 没写黑夜模式、刺眼！😂

&emsp;&emsp;我发现 [ipinfo.io](https://ipinfo.io) 的数据库很准确、支持查询 IPv6、调用其 API 可以获得 IP 的厂商信息及 ASN 等信息。那就干脆自己手撸一个前端 VUE 页面，自己写按照地区和 ASN 分类的表格函数，也支持表格里一行一个的 IP 直接复制。满足了自己的需求，如果你也想有一个这样的批量 IP 查询站，那可以用 Docker 在自己的机器上部署一个玩玩儿~。

&emsp;【[我的此项目 GitHub 开源地址（就在这里）](https://github.com/EzXxY/VUE)】&emsp;【[我的此项目 Docker Hub 镜像地址](https://hub.docker.com/r/ezxxy/vue-ip/tags)】

（欢迎下载源码，自己修改 CSS 样式等，可以给我来一个免费的 ✨Star🌟 呦！~）

> &emsp;简单看看用 **站长之家** 和 **UU在线工具** 批量查 IP 的***痛点！***（如果有其他好用的，请提Issues，谢谢~）

![站长之家查IP.png](https://ezxxy.github.io/img/04-批量查询IP地区/站长之家查IP.png)
![UU在线工具查IP.png](https://ezxxy.github.io/img/04-批量查询IP地区/UU在线工具查IP.png)

> # 我自己写的批量查 IP 的 VUE 前端网页：（国家/地区；ASN；支持 IPv6；按需复制）😍

![演示_1.png](https://ezxxy.github.io/img/04-批量查询IP地区/演示_1.png)
![演示_2.png](https://ezxxy.github.io/img/04-批量查询IP地区/演示_2.png)


# 2. Docker 部署

&emsp;&emsp;本项目占用空间很小，基于最基本的 Nginx 镜像，总共只有 17.51 MB。我建议大家使用 Docker-compose 部署本项目，当然我会贴出 Docker run 的长命令。本项目支持昼夜模式的变化，默认跟随系统。你可以部署后切换系统的昼夜模式看看，会自动跟随其发生改变。

![Docker-Hub.jpg](https://ezxxy.github.io/img/04-批量查询IP地区/Docker-Hub.jpg)

&emsp;&emsp;因为使用的是 **ipinfo.io** 的 **API**，所以在开始部署之前，需要大家登录 【[https://ipinfo.io/login](https://ipinfo.io/login)】 查看自己账号的 TOKEN，每个账号下的 TOKEN 每个月可以查询 **50000** 条 IP，当前 TOKEN 用量的查询也在 [这个网页](https://ipinfo.io/login) 。

&emsp;&emsp;在网页中选择使用 Google 账号或者 GitHub 账号进行登录，或者选用临时邮箱注册登录都可以。登录之后下拉到 **【Step 3】** 的位置，即可复制自己的 TOKEN，如下图所示：

![登录查看token.jpg](https://ezxxy.github.io/img/04-批量查询IP地区/登录查看token.jpg)
![复制自己的TOKEN.jpg](https://ezxxy.github.io/img/04-批量查询IP地区/复制自己的TOKEN.jpg)

&emsp;&emsp;&emsp;本月我的某账号下的 TOKEN 用量情况如下，已经查询了 4321 条，还可以查询 45679 条：

![TOKEN用量查询.jpg](https://ezxxy.github.io/img/04-批量查询IP地区/TOKEN用量查询.jpg)

## &emsp; 2-1. 使用 Docker-compose 部署

&emsp;&emsp;&emsp;确保自己已经安装了 Docker 和 Docker-compose，在任意你喜欢的路径下，新建一个 docker-compose.yml 文件。请把 **7845** 换成自己喜欢且还没有被占用的端口，并且把刚刚复制的 **TOKEN** 值替换掉。你也可以自定义网页内容所占屏幕的宽度，上面演示的效果所使用的比例就是默认的 **70%**。docker-compose.yml 文件内容如下：

```
# 用什么新建文件都行，可以使用 nano
vim docker-compose.yml
```

```
version: '3.8'
services:
  vue-ip:
    image: ezxxy/vue-ip:latest
    restart: always
    ports:
      - "7845:7840"                 	# 7845 可以改成目前没有被占用且自己喜欢的端口号
    environment:
      - TOKEN=替换为刚刚复制的 TOKEN    # TOKEN 改成自己 ipinfo.io 账号下的，刚刚复制的
      - WIDTH=70%                       # 自定义网页内容占据屏幕的宽度，看自己需要，不用改就行
    container_name: ezxxy-vue-ip
```
```
docker-compose up -d
```

## &emsp; 2-2. 使用 Docker run 命令部署

&emsp;&emsp;&emsp;不想用 Docker-compose，而是想用 Docker 直接部署的小伙伴可以使用如下的命令，请把 **7845** 改成自己喜欢且没有被占用的端口号，TOKEN 中的值请用自己的：


```
docker run -d --restart=always -p 7845:7840 --name ezxxy-vue-ip -e "TOKEN=换成自己刚刚复制的TOKEN" -e "WIDTH=70%" ezxxy/vue-ip:latest
```
&emsp;

# 3. 开放端、并配置反向代理，用域名解析并开启 https 访问


```
# 如果你用的是 ufw 管理防火墙，开放上面的设置的端口
ufw allow 7845
ufw reload
```

&emsp;&emsp;这样你就可以使用 【[http://服务器的公网IP:7845](http://服务器的公网IP:7845)】 来访问了。如果想要配置反向代理，可以使用 **Nginx Proxy Manager**、宝塔面板反向代理、手搓 Nginx 等。下面演示的是用 NPM 来配置反向代理（这个很好用，在一台机器上部署了 NPM，可以给任意机器上的站点配置 SSL 证书并使用域名开启 https 访问）。搭建的链接参考 【[此 BiliBili 视频](https://www.bilibili.com/video/BV1Gg411w7kQ)】


![NPM_1.jpg](https://ezxxy.github.io/img/04-批量查询IP地区/NPM_1.jpg)
![NPM_2.jpg](https://ezxxy.github.io/img/04-批量查询IP地区/NPM_2.jpg)

> # &emsp; 🥰 我的演示站点：[https://ip.ezxxy.me](https://ip.ezxxy.me)

![网页.jpg](https://ezxxy.github.io/img/04-批量查询IP地区/网页.jpg)


