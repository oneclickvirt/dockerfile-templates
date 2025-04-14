FROM ubuntu:22.04

# 设置环境变量
ENV DEBIAN_FRONTEND=noninteractive \
    TZ=Asia/Shanghai \
    PATH="/opt/conda/bin:$PATH"

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

# 安装系统依赖
RUN apt-get update && apt-get install -y \
    wget curl git vim \
    python3 python3-pip

# 复制必要的文件
COPY dcmi /usr/local/dcmi
COPY npu-smi /usr/local/bin/npu-smi
COPY common /usr/local/Ascend/driver/lib64/common
COPY driver /usr/local/Ascend/driver/lib64/driver
COPY ascend_install.info /etc/ascend_install.info
COPY vnpu.cfg /etc/vnpu.cfg
COPY version.info /usr/local/Ascend/driver/version.info
RUN chmod +x /usr/local/bin/npu-smi

ENV USER_ID=0
ENV GROUP_ID=0
WORKDIR /home/ubuntu/

# 安装 Miniconda
RUN wget -O /tmp/miniconda.sh https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh && \
    bash /tmp/miniconda.sh -b -p /opt/conda && \
    rm /tmp/miniconda.sh

# 初始化 Conda 并创建 Jupyter 环境
RUN /opt/conda/bin/conda init bash && \
    echo "source /opt/conda/bin/activate" >> ~/.bashrc && \
    conda config --set auto_activate_base false && \
    conda create -n jupyter_env python=3.10 -y && \
    conda clean -afy

# 安装 Jupyter 及插件
RUN /opt/conda/bin/conda run -n jupyter_env pip install --no-cache-dir \
    jupyterlab notebook jupyter_server jupyter_http_over_ws jupyter-ai[all] && \
    /opt/conda/bin/conda run -n jupyter_env jupyter server extension enable --py jupyter_http_over_ws

# 写入 Jupyter 配置文件
RUN mkdir -p /root/.jupyter && \
    echo "c.ServerApp.allow_origin = '*'" >> /root/.jupyter/jupyter_server_config.py && \
    echo "c.ServerApp.ip = '0.0.0.0'" >> /root/.jupyter/jupyter_server_config.py && \
    echo "c.ServerApp.port = 31000" >> /root/.jupyter/jupyter_server_config.py && \
    echo "c.ServerApp.allow_remote_access = True" >> /root/.jupyter/jupyter_server_config.py && \
    echo "c.ServerApp.open_browser = False" >> /root/.jupyter/jupyter_server_config.py && \
    echo "c.ServerApp.token = ''" >> /root/.jupyter/jupyter_server_config.py && \
    echo "c.ServerApp.password = ''" >> /root/.jupyter/jupyter_server_config.py && \
    echo "c.ServerApp.disable_check_xsrf = True" >> /root/.jupyter/jupyter_server_config.py

RUN echo 'export LD_LIBRARY_PATH=/usr/local/Ascend/driver/lib64/driver:/usr/local/Ascend/driver/lib64:/usr/local/Ascend/driver/lib64/common:/usr/local/Ascend/develop/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc

# 设置默认 home 目录
ENV HOME=/home/ubuntu/

# 暴露端口
EXPOSE 31000

CMD ["bash", "-c", "conda init bash && source ~/.bashrc && conda activate jupyter_env && jupyter lab --allow-root --port=31000 --ip=0.0.0.0 --NotebookApp.token='' --NotebookApp.password=''"]
