# shadowsocks-server-client-proxy

## shadowsocks-server 

* 使用docker-compose 安装 ，默认aes-256-cfb

* 镜像地址https://hub.docker.com/r/benyoo/shadowsocks/

```
version: '2'
services:
  shadowsocks:
    container_name: shadowsocks_server
    restart: always
    image: benyoo/shadowsocks:latest
    ports:
    - "10080:10080"
    environment:
    - MANYUSER=No
    - SERVER=0.0.0.0
    - SERVER_PORT=10080
    - PASSWORD=
```

* 使用docker 命令行安装
* 镜像地址https://hub.docker.com/r/mritd/shadowsocks/

## shadowsocks-client

* 使用docker 命令行安装
* 镜像地址https://hub.docker.com/r/mritd/shadowsocks/

```
docker run -dt --name ssclient -p 1080:1080 mritd/shadowsocks -m "ss-local" -s "-s 127.0.0.1 -p 6500 -b 0.0.0.0 -l 1080 -m aes-256-cfb -k test123 --fast-open" -x -e "kcpclient" -k "-r SSSERVER_IP:6500 -l :6500 -mode fast2"
```

## proxy 安装

* centos场景
* 安装privoxy

```
yum install privoxy -y
```

* 安装完成后，修改privoxy配置文件

```
vim /etc/privoxy/config 
```

* 修改内容如下：

```
forward-socks5t   /               127.0.0.1:1080 .
listen-address  127.0.0.1:8118
```

* 启动privoxy

```
privoxy /etc/privoxy/config
```

* 可以使用 `ps aux|grep privoxy`和 `lsof -i:8118`来检查是否成功启动
* 正常情况下,可以使用http代理了，代理地址`http://127.0.0.1:8118`

## 示例

例如，可以给 git 设置 http 或 https 代理,设置方式如下：

```
git config --global http.proxy http://127.0.0.1:8118
git config --global https.proxy http://127.0.0.1:8118
```

取消代理配置：

```
git config --global --unset http.proxy
git config --global --unset https.proxy
```

* 和其他 git 的配置一样，不使用命令行而是直接修改相应的 `.gitconfig` 文件也是可以的。当然，如果不想修改 git 配置，而只是想临时使用一下，可以使用 -c 参数：

```
git -c https.proxy=http://127.0.0.1:8118 clone --depth=1 https://github.com/xxx/xxx
```