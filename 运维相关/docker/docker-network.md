# network 

## 简单指令

```bash
# 查看所有network
docker network ls

# 创建 network
docker network create network_name

# 查看信息
docker network inspect network_name

# 删除network
docker netwo rm network_name
```

## 创建

```bash
# 再次创建 network1，指定子网
docker network create -d bridge --subnet=192.168.16.0/24 --gateway=192.168.16.1 network1
```

运行容器时指定网络

```bash
docker run -d --network netwrok_name container_name
```

## 修改

将容器连接到网络

```bash
docker network connect network_name container_name
```

## 模式

### bridge

为每一个容器分配并配置ip，将容器连接到**docker0**这样一个虚拟网桥，这种方式为默认方式

会在本机上多创建一个veth地址，在容器内部多一个eth0地址

存在c1和c2两个容器，杀死c1之后，重启c2，容器的ip可能发生改变，c2的ip会变为c1的ip，这种情况下就不能通过ip地址完成容器的访问。

### host

这种模式直接连接到了宿主机，进入容器后的地址信息就是宿主机的地址信息，容器可以理解为一个服务，跑在宿主机的端口上

### none

没有网卡、IP和路由等信息，只有一个回环地址，需要我们自己添加相应的信息

### container

和已经存在的容器公用网络信息，例如A容器端口8080映射为外面的8081，有B容器使用了A容器的9000，则访问A容器的9000就能找到B

## 自定义网络

docker默认采用的是bridge模式，当创建量个容器之后，会分配对应的ip地址，进入容器之后可以互相ping通，但是ping容器名没法ping通，并且这种情况下容器的ip地址有可能发生改变。

可以创建网络（创建的网络默认是bridge模式），创建之后ip地址和容器名称都可以ping通。