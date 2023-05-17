# docker部署

## 注意问题

1. jenkins最新的镜像为jenkins/jenkins
2. 需要将jenkins_home中的uid为1000，要通过`chown -R 1000:1000  /jenkins_home`命令修改权限

