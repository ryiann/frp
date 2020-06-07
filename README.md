# FRP

[GitHub FRP][1]

frp 是一个可用于内网穿透的高性能的反向代理应用，支持 tcp, udp 协议，为 http 和 https 应用协议提供了额外的能力，且尝试性支持了点对点穿透。

为了方便在不同的系统中安装和配置frp，我基于docker对frp server、client进行了封装和打包。

## 下载

* frp server

```
docker pull ryaning/frps
```

* frp client

```
docker pull ryaning/frpc
```

## 使用

* touch frps.ini

```
touch /etc/frp/frps.ini

cat > /etc/frp/frps.ini << EOF
[common]
bind_port = 7000
EOF
```

* start frps

```
docker run --restart=always --network host -v /etc/frp/frps.ini:/etc/frp/frps.ini --name frps -d ryaning/frps
```

* touch frpc.ini

```
touch /etc/frp/frpc.ini

cat > /etc/frp/frpc.ini << EOF
[common]
server_addr = 127.0.0.1
server_port = 7000

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000
EOF
```

* start frpc

```
docker run --restart=always --network host -v /etc/frp/frpc.ini:/etc/frp/frpc.ini --name frpc -d ryaning/frpc
```

* **命令说明**
    * `--restart=always`：当 docker 重启时，容器自动启动。
    * `--network host`：容器使用宿主机的网络，指定我们的网络模式为host，这样我们访问本机的端口就能访问到我们的容器。

## 举例

远程桌面：在A地拥有windows电脑B，在C地拥有windows电脑D，以及拥有公网服务器E，目标是实现内网穿透使电脑D能够远程电脑B。

### 操作如下

* 1、在公网服务器E上编写好`frps.ini`配置文件，然后启动frp server。

* frps.ini

请根据情况更改配置。

```
[common]
# 服务占用端口
bind_port = 7000
# 服务认证密码
auto_token=123456

# 状态查询页面占用端口
dashboard_port = 7500
# 状态查询页面用户名
dashboard_user = admin
# # 状态查询页面密码
dashboard_pwd = admin

# 服务器映射的域名
subdomain_host = xxx.xxx.com
```

* start server

```
docker run --restart=always --network host -v /etc/frp/frps.ini:/etc/frp/frps.ini --name frps -d ryaning/frps
```

当服务启动完成，登录状态查询页面（http://ip:7500），可以查询具体状态。

<img src="https://image.ryana.cn/uploads/big/b0e321822cdd794445fa2e52f3eecb45.png" width="80%">

2、在电脑B上修改/编写`frpc.ini`配置文件，并开启frp client服务。

* frpc.ini

```
[common]
# 服务端地址
server_addr = xxx.xxx.com
# 服务端口
server_port = 7000

# 名称唯一即可
[mstsc]
type = tcp
local_ip = 127.0.0.1
# 本地端口
local_port = 3389
# 远程端口
remote_port = 3389
```

* start frpc

下载windows版 frp client，例：[frp_0.31.2_windows_amd64.zip][2]，然后使用`frpc.exe -c frpc.ini`命令启动；因为目前docker for windows不支持`--network host`参数，所以暂时采取`frpc.exe`方式来运行。

3、在D电脑上进行远程连接

在D电脑上打开远程连接，输入对应的<u>服务IP:远程端口</u>（`server_addr:remote_port`）即可远程。

**tips:** frp支持Linux、Windows、Mac平台。[goto][3]

[1]:https://github.com/fatedier/frp/blob/master/README_zh.md
[2]:https://github.com/fatedier/frp/releases/download/v0.31.2/frp_0.31.2_windows_amd64.zip
[3]:https://github.com/fatedier/frp/releases
