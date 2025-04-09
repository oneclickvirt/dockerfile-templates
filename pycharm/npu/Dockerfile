# 基础镜像
FROM docker.imgdb.de/jlesage/baseimage-gui:ubuntu-22.04-v4

# 更新系统并安装必要的依赖
RUN apt-get update && apt-get install -y \
    openjdk-17-jdk \
    libxrender1 \
    libxtst6 \
    libxi6 \
    libxext6 \
    libfontconfig1 \
    libfreetype6 \
    libx11-6 \
    git \
    vim \
    nano \
    sudo \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# 设置 JAVA_HOME
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-arm64
ENV PATH=$JAVA_HOME/bin:$PATH

ENV LD_LIBRARY_PATH=/usr/local/Ascend/driver/lib64/common:$LD_LIBRARY_PATH
ENV LD_LIBRARY_PATH=/usr/local/Ascend/driver/lib64/driver:$LD_LIBRARY_PATH

# 拷贝 PyCharm 安装包及相关文件
COPY pycharm-community-2024.1-aarch64.tar.gz /tmp/pycharm.tar.gz
COPY dcmi /usr/local/dcmi
COPY npu-smi /usr/local/bin/npu-smi
COPY common /usr/local/Ascend/driver/lib64/common
COPY driver /usr/local/Ascend/driver/lib64/driver
COPY ascend_install.info /etc/ascend_install.info
COPY vnpu.cfg /etc/vnpu.cfg
COPY version.info /usr/local/Ascend/driver/version.info

# 解压 PyCharm 并创建快捷方式
RUN tar -xzf /tmp/pycharm.tar.gz -C /opt && \
    ln -s /opt/pycharm-community-2024.1/bin/pycharm.sh /usr/local/bin/pycharm && \
    rm /tmp/pycharm.tar.gz

# 创建启动脚本
RUN echo '#!/bin/sh' > /startapp.sh && \
    echo 'exec pycharm' >> /startapp.sh && \
    chmod +x /startapp.sh

# 确保运行程序的用户非app用户
ENV USER_ID=0
ENV GROUP_ID=0

# 修复 /etc/passwd 中的 app 用户问题
RUN sed -i '/app::/d' /etc/passwd

# 设置工作目录
WORKDIR /home/ubuntu/

# 设置端口环境变量
ENV WEB_LISTENING_PORT=31000
ENV VNC_LISTENING_PORT=31002

# 配置版本信息
RUN echo "APP_NAME=PyCharm Community" >> /etc/environment && \
    echo "APP_VERSION=2024.1" >> /etc/environment

# 修改默认的pycharm配置
RUN tee -a /opt/pycharm-community-2024.1/bin/idea.properties <<EOF

idea.system.path=/home/ubuntu/system
idea.config.path=/home/ubuntu/config
idea.plugins.path=/home/ubuntu/plugins
idea.default.project.directory=/home/ubuntu/
idea.no.launcher.banner=true
idea.disable.protocol.confirmation=true
EOF

# 设置默认 home 目录
ENV HOME=/home/ubuntu/
