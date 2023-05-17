# Dockerfile

该文件指示了如何构建一个镜像

## 运行

```bash
docker build -t imageName:tagName .
```

在run中的参数

  `-v`或者`--volume`


  - 由三个由冒号（:）分隔的字段组成，`[HOST-DIR:]CONTAINER-DIR[:OPTIONS]`。
  - `HOST-DIR` 代表主机上的目录或数据卷的名字。省略该部分时，会自动创建一个匿名卷。如果是指定主机上的目录，需要使用绝对路径。也可以使用volume的名字 。
  - `CONTAINER-DIR` 代表将要挂载到容器中的目录或文件，即表现为容器中的某个目录或文件
  - `OPTIONS` 代表权限配置，

      - 只读权限（`ro`）
      - 只有该容器可以使用（`Z`）
      - 可以被多个容器使用（`z`）
      - 多个配置项由逗号分隔。

```bash
docker container run \
    -it \
    --name container_name \
    -v volume1:/volume1 \
    --rm ubuntu /bin/bash
```

## 书写Dockerfile

```dockerfile
# 指定来自哪个镜像
FROM openjdk:11.0.15
# 创建文件+ 改工作路径
RUN mkdir /jarPackage
RUN cd jarPackage
WORKDIR /jarPackage
# 将jar包复制到容器内
COPY  cz-test-0.0.1-SNAPSHOT.jar /jarPackage
# 启动容器时执行
ENTRYPOINT ["java","-jar","./cz-test-0.0.1-SNAPSHOT.jar"]
# 暴露端口
EXPOSE 9000
```

### RUN和CMD的区别

run执行的是在**构建过程中**需要执行的命令

CMD中的命令是**在镜像运行**的时候，执行的命令

### WORKDIR

指定工作路径，其他相对路径都没有改变（如果从根目录写起，例如`WORKDIR /app`之后，/work代表的还是根目录下的work而不是/app/work,如果想要表示/app/work,需要使用`./work`，主要改变工作路径只改变了`.`指示的东西。)