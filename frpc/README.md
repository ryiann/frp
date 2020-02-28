# FRPS

### FRP_S镜像

- 基于官方centos镜像
- 开启ssh服务，并初始化root密码为123456

### 使用

```
docker pull ryaning/centos-ssh
```

### 启动

```
docker run --name centos-ssh-001 \
-d ryaning/centos-ssh:latest
```
或者

```
docker run --privileged -ti \
--name centos-ssh-001 \
-p 22:22 \
-d ryaning/centos-ssh:latest /usr/sbin/init
```
