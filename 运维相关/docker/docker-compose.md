# docker-compose

## 示例文件

```yaml
version: "3.3"
services:
  cz-website-backend-v1:
    image: "cz-website-back:v1.0"
    ports:
      - "10000:9000"
```

image一定要指定对名字，否则就会从dockerhub拉取。

指定volumes之后，最后一定要对使用的volume进行声明。

>`version: '3'`这种语法中`version`单词后面不加空格，但是冒号后面要添加空格，剩下的也都是这种格式

```yaml
version: '3.3'
services:
    mysql:
        container_name: "blog-system-mysql"
        network_mode: "host"
        environment:
            MYSQL_ROOT_PASSWORD: "123456"
            MYSQL_USER: 'root'
            MYSQL_PASS: '123456'
        image: "mysql:5.7.30"
        restart: always
        ports:
            - 3306:3306
        volumes:
            - "/db:/var/lib/mysql"
            - "/root/docker/mysql/conf:/etc/mysql"
            - "/root/docker/mysql/log:/var/log/mysql"
```



## 运行

```bash
docker-compose up  -d
```

