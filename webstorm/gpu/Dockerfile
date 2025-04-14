# 基础镜像
FROM jlesage/baseimage-gui:ubuntu-22.04-v4

# 添加中文语言和字体支持
RUN apt-get update && apt-get install -y \
    language-pack-zh-hans \
    fonts-noto-cjk \
    fonts-wqy-microhei \
    && locale-gen zh_CN.UTF-8 && \
    update-locale LANG=zh_CN.UTF-8

ENV LANG=zh_CN.UTF-8
ENV LANGUAGE=zh_CN:zh
ENV LC_ALL=zh_CN.UTF-8

# 更新系统并安装必要的依赖
RUN apt-get update && apt-get install -y \
    python3-pip \
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
    build-essential \
    dkms \
    curl \
    initramfs-tools

# 设置 JAVA_HOME
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-arm64
ENV PATH=$JAVA_HOME/bin:$PATH

# 拷贝 webstorm 安装包及相关文件
COPY WebStorm-2024.1.tar.gz /tmp/WebStorm.tar.gz
COPY NVIDIA-Linux-x86_64-550.135.run /tmp/NVIDIA-Linux-x86_64-550.135.run

# 禁用 Nouveau 驱动
RUN echo -e "blacklist nouveau\noptions nouveau modeset=0" > /etc/modprobe.d/blacklist-nouveau.conf && \
    update-initramfs -u

# 安装 NVIDIA 550.135 驱动（自动化、静默安装）
RUN chmod +x /tmp/NVIDIA-Linux-x86_64-550.135.run && \
    /tmp/NVIDIA-Linux-x86_64-550.135.run --no-kernel-module --silent --accept-license

# 解压 webstorm 并创建快捷方式
RUN tar -xzf /tmp/WebStorm.tar.gz -C /opt && \
    ln -s /opt/WebStorm-241.14494.235/bin/webstorm.sh /usr/local/bin/webstorm && \
    rm /tmp/WebStorm.tar.gz

# 创建启动脚本
RUN echo '#!/bin/sh' > /startapp.sh && \
    echo 'exec webstorm' >> /startapp.sh && \
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
RUN echo "APP_NAME=webstorm Community" >> /etc/environment && \
    echo "APP_VERSION=2024.1" >> /etc/environment

# 修改默认的webstorm配置
RUN echo 'idea.system.path=/home/ubuntu/system' >> /opt/WebStorm-241.14494.235/bin/idea.properties && \
    echo 'idea.config.path=/home/ubuntu/config' >> /opt/WebStorm-241.14494.235/bin/idea.properties && \
    echo 'idea.plugins.path=/home/ubuntu/plugins' >> /opt/WebStorm-241.14494.235/bin/idea.properties && \
    echo 'idea.default.project.directory=/home/ubuntu/' >> /opt/WebStorm-241.14494.235/bin/idea.properties && \
    echo 'idea.no.launcher.banner=true' >> /opt/WebStorm-241.14494.235/bin/idea.properties && \
    echo 'idea.disable.protocol.confirmation=true' >> /opt/WebStorm-241.14494.235/bin/idea.properties

# 设置默认 home 目录
ENV HOME=/home/ubuntu/
