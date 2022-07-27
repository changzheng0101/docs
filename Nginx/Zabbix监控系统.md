# Zabbix

主要对系统的行为进行监视



Zabbix 由以下几个主要软件组件构成：

- Server
- 存储数据库
- Web 界面
- Agent 监控代理
- ![img](https://doc.shiyanlou.com/courses/uid977658-20191008-1570517884264)
- **Zabbix Agent** 监控代理部署在监控目标上，能够主动监控本地资源和应用程序，并将收集到的数据报告给 Zabbix Server，完成信息的收集



```bash
# 确保当前工作路径在家目录
cd ~

# 下载安装包
wget https://labfile.oss.aliyuncs.com/courses/1403/zabbix-release_5.2_all.deb
# 安装
sudo dpkg -i zabbix-release_5.2_all.deb
sudo vim /etc/apt/sources.list.d/zabbix.list
deb http://mirrors.cloud.aliyuncs.com/zabbix/zabbix/5.2/ubuntu focal main
deb-src http://mirrors.cloud.aliyuncs.com/zabbix/zabbix/5.2/ubuntu focal main
# 更新源
sudo apt update

sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf
```

创建对应的`zabbix`数据库

```sql
CREATE DATABASE zabbix CHARACTER SET utf8 COLLATE utf8_bin;
USE zabbix;
SHOW TABLES;
create user zabbix@localhost identified by 'zabbixPSWD';
grant all privileges on zabbix.* to zabbix@localhost;
```

