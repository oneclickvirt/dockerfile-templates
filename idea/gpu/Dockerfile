# 基础镜像
FROM docker.imgdb.de/jlesage/baseimage-gui:ubuntu-22.04-v4

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
    initramfs-tools \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# 设置 JAVA_HOME
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH

# 拷贝 idea 安装包及相关文件
COPY ideaIC-2024.2.1.tar.gz /tmp/idea.tar.gz
COPY NVIDIA-Linux-x86_64-550.135.run /tmp/NVIDIA-Linux-x86_64-550.135.run

# 解压 idea 并创建快捷方式
RUN tar -xzf /tmp/idea.tar.gz -C /opt && \
    ln -s /opt/idea-IC-242.21829.142/bin/idea.sh /usr/local/bin/idea && \
    rm /tmp/idea.tar.gz

# 禁用 Nouveau 驱动
RUN echo -e "blacklist nouveau\noptions nouveau modeset=0" > /etc/modprobe.d/blacklist-nouveau.conf && \
    update-initramfs -u

# 安装 NVIDIA 550.135 驱动（自动化、静默安装）
RUN chmod +x /tmp/NVIDIA-Linux-x86_64-550.135.run && \
    /tmp/NVIDIA-Linux-x86_64-550.135.run --no-kernel-module --silent --accept-license

# 创建启动脚本
RUN echo '#!/bin/sh' > /startapp.sh && \
    echo 'exec idea' >> /startapp.sh && \
    chmod +x /startapp.sh

# 确保运行程序的用户非 app 用户
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
RUN echo "APP_NAME=Idea Community" >> /etc/environment && \
    echo "APP_VERSION=2024.2.1" >> /etc/environment

# 修改默认的 idea 配置
RUN tee -a /opt/idea-IC-242.21829.142/bin/idea.properties <<EOF

idea.system.path=/home/ubuntu/system
idea.config.path=/home/ubuntu/config
idea.plugins.path=/home/ubuntu/plugins
idea.default.project.directory=/home/ubuntu/
idea.no.launcher.banner=true
idea.disable.protocol.confirmation=true
EOF

# 设置默认 home 目录
ENV HOME=/home/ubuntu/
