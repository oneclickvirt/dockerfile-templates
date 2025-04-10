# 基础镜像
FROM jlesage/baseimage-gui:ubuntu-22.04-v4

# 安装必要依赖（减少重复）
RUN apt-get update && \
    apt-get install -y \
    language-pack-zh-hans \
    fonts-noto-cjk-extra \
    openjdk-17-jdk \
    git \
    vim \
    nano \
    build-essential \
    libgtk-3-0 \
    libx11-6 \
    libxext6 \
    libxrender1 \
    libxtst6 \
    libxi6 \
    libgl1-mesa-glx \
    libglib2.0-0 \
    mesa-utils \
    && locale-gen en_US.UTF-8 \
    && update-locale LANG=en_US.UTF-8

# 设置语言和 JAVA 环境
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

# 配置 VNC 服务器参数
ENV DISPLAY=:0
# 允许自动登录
ENV GUI_AUTOLOGIN=1
# 使用 root 用户启动 Eclipse
ENV GUI_USER=root
# VNC 端口（用于 VNC 客户端）
ENV VNC_PORT=5900
# noVNC 端口（用于 Web 浏览器访问）
ENV WEB_LISTENING_PORT=31000
# 启用剪贴板共享
ENV ENABLE_CLIPBOARD=1

# 拷贝 Eclipse 安装包
COPY eclipse-jee-2025-03-R-linux-gtk-aarch64.tar.gz /tmp/eclipse.tar.gz

# 解压 Eclipse 并配置
RUN tar -xzf /tmp/eclipse.tar.gz -C /opt && \
    ln -s /opt/eclipse/eclipse /usr/local/bin/eclipse && \
    rm /tmp/eclipse.tar.gz

# 配置启动脚本以使用 root 用户
RUN echo '#!/bin/bash' > /startapp.sh && \
    echo 'export HOME="/home/ubuntu/"' >> /startapp.sh && \
    echo 'exec /usr/local/bin/eclipse -debug' >> /startapp.sh && \
    chmod +x /startapp.sh

RUN chmod +x /startapp.sh

# 配置桌面快捷方式
RUN mkdir -p /defaults/shortcuts && \
    echo "[Desktop Entry]" > /defaults/shortcuts/eclipse.desktop && \
    echo "Name=Eclipse" >> /defaults/shortcuts/eclipse.desktop && \
    echo "Exec=/usr/local/bin/eclipse" >> /defaults/shortcuts/eclipse.desktop && \
    echo "Icon=/opt/eclipse/icon.xpm" >> /defaults/shortcuts/eclipse.desktop && \
    echo "Type=Application" >> /defaults/shortcuts/eclipse.desktop && \
    echo "Categories=Development;IDE;" >> /defaults/shortcuts/eclipse.desktop

# 确保运行程序的用户非 app 用户
ENV USER_ID=0
ENV GROUP_ID=0

# 修复 /etc/passwd 中的 app 用户问题
RUN sed -i '/app::/d' /etc/passwd

# 设置工作目录
WORKDIR /home/ubuntu/

# 设置默认 home 目录
ENV HOME=/home/ubuntu/

# 暴露端口
EXPOSE 5900 31000

# 继承基础镜像的 CMD
CMD ["/init"]