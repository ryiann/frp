# 基础镜像
FROM amd64/alpine:3.15
# 作者
LABEL maintainer="ryan <me@ryana.cn>"
# # 定义构建参数
ENV FRP_VERSION 0.44.0
# 构建镜像
RUN cd /root \
    &&  wget --no-check-certificate -c https://github.com/fatedier/frp/releases/download/v${FRP_VERSION}/frp_${FRP_VERSION}_linux_amd64.tar.gz \
    &&  tar zxvf frp_${FRP_VERSION}_linux_amd64.tar.gz  \
    &&  cd frp_${FRP_VERSION}_linux_amd64/ \
    &&  cp frpc /usr/bin/ \
    &&  mkdir -p /etc/frp \
    &&  cp frpc.ini /etc/frp \
    &&  cd /root \
    &&  rm frp_${FRP_VERSION}_linux_amd64.tar.gz \
    &&  rm -rf frp_${FRP_VERSION}_linux_amd64/
# 启动时运行
ENTRYPOINT /usr/bin/frpc -c /etc/frp/frpc.ini
