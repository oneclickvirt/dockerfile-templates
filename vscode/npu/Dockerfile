# 基础镜像
FROM ubuntu:22.04

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
    libxrender1 \
    libxtst6 \
    libxi6 \
    libxext6 \
    libfontconfig1 \
    libfreetype6 \
    libx11-6 \
    libatomic1 \
    git \
    vim \
    nano \
    sudo

ENV LD_LIBRARY_PATH=/usr/local/Ascend/driver/lib64/common:$LD_LIBRARY_PATH
ENV LD_LIBRARY_PATH=/usr/local/Ascend/driver/lib64/driver:$LD_LIBRARY_PATH

# 拷贝 code-server 安装包及相关文件
COPY code-server-4.98.0-linux-arm64.tar.gz /tmp/code-server.tar.gz
COPY dcmi /usr/local/dcmi
COPY npu-smi /usr/local/bin/npu-smi
COPY common /usr/local/Ascend/driver/lib64/common
COPY driver /usr/local/Ascend/driver/lib64/driver
COPY ascend_install.info /etc/ascend_install.info
COPY vnpu.cfg /etc/vnpu.cfg
COPY version.info /usr/local/Ascend/driver/version.info
RUN chmod +x /usr/local/bin/npu-smi

# 解压 code-server 并创建快捷方式
RUN tar -xzf /tmp/code-server.tar.gz -C /opt && \
    ln -s /opt/code-server-4.98.0-linux-arm64/bin/code-server /usr/local/bin/code-server && \
    rm /tmp/code-server.tar.gz

# 设置端口环境变量
ENV CODE_SERVER_PORT=31000

# 创建启动脚本，确保 code-server 使用正确的配置
RUN echo '#!/bin/sh' > /startapp.sh && \
    echo 'PORT=${CODE_SERVER_PORT:-31001}' >> /startapp.sh && \
    echo 'mkdir -p /config/xdg/config/code-server' >> /startapp.sh && \
    echo 'echo "bind-addr: 0.0.0.0:$PORT" > /config/xdg/config/code-server/config.yaml' >> /startapp.sh && \
    echo 'echo "auth: none" >> /config/xdg/config/code-server/config.yaml' >> /startapp.sh && \
    echo 'echo "cert: false" >> /config/xdg/config/code-server/config.yaml' >> /startapp.sh && \
    echo 'export XDG_CONFIG_HOME=/config/xdg/config' >> /startapp.sh && \
    echo 'exec code-server --bind-addr 0.0.0.0:$PORT' >> /startapp.sh && \
    chmod +x /startapp.sh

# 确保运行程序的用户非 app 用户
ENV USER_ID=0
ENV GROUP_ID=0

# 修复 /etc/passwd 中的 app 用户问题
RUN sed -i '/app::/d' /etc/passwd

# 设置工作目录
WORKDIR /home/ubuntu/

# 配置版本信息
RUN echo "APP_NAME=code-server" >> /etc/environment && \
    echo "APP_VERSION=4.98.0" >> /etc/environment

# 设置默认 home 目录
ENV HOME=/home/ubuntu/

# 暴露端口
EXPOSE 31000

CMD ["/startapp.sh"]
