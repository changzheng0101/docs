# 什么是Nginx

Nginx 是一个 **高性能的代理服务器**，能够反向代理 `HTTP`、 `HTTPS`、`SMTP`、 `POP3`、 `IMAP` ，也可以作为一个负载均衡器和 HTTP 缓存。同时，它还是一个免费的、开源的、高性能的 HTTP 服务器。

## 启动脚本

```bash
sudo service nginx start
sudo service nginx status
nginx -V
# 格式化输出
nginx -V 2>&1 | sed 's/ /\n/g'

#nginx -V 2>&1 | sed 's/ /\n/g'

#cat /etc/nginx/nginx.conf | grep -vE "#|^$" 
# 这里存放events 和 Http 两个配置
# 通常的Server要写成单独的配置
# 去除#和空白行
```

![img](https://dn-simplecloud.shiyanlou.com/uid/276733/1517307303225.png)

```bash
# 查看某一个server配置
cd /etc/nginx/sites-enabled/

cat ./default | grep -vE "#|^$"
```



`https://www.lanqiao.cn/louplus/linux`，`https://` 是协议，`www.lanqiao.cn` 是域名，`/louplus/linux` 是 URI

location是对URI来进行匹配

location 配置语法

```bash
location [ = | ~ | ~* | ^~ ] pattern {
#    ......
#    ......
}
```

- `=`：用于精准匹配，想请求的 URI 与 pattern 表达式完全匹配的时候才会执行 location 中的操作
- `~`：用于区分大小写的正则匹配；
- `~*`：用于不区分大小写的正则匹配；
- `^~`：用于匹配 URI 的前半部分；

```bash
try_files $uri $uri/ =404;
```

一共查找了三个地方

1. $uri
2. $uri/
3. 都不存在返回404

检查nginx配置文件是否有语法错误

```bash
sudo nginx -t
```

重启nginx

```bash
sudo service nginx restart
```





部署产看nginx状态的网站

```bash

server {
    listen 8080 default_server;

    server_name localhost;

    location /nginx_status {
        stub_status on;
    }
}
```

