# 基础镜像

ubuntu:22.04

# 本地文件下载地址

| Item                              | Link                                                                                      |
|-----------------------------------|-------------------------------------------------------------------------------------------|
| NVIDIA-Linux-x86_64-550.135.run | [Download Link](https://cn.download.nvidia.com/XFree86/Linux-x86_64/550.135/NVIDIA-Linux-x86_64-550.135.run)  |
| code-server-4.98.0-linux-amd64.tar.gz | [Download Link](https://github.com/coder/code-server/releases/download/v4.98.0/code-server-4.98.0-linux-amd64.tar.gz)   |

# Vscode + CUDA + PyTorch + AI插件 镜像构建流程  

## 1. 下载必要文件  
在当前目录下下载以下文件：  
- `Dockerfile`（本仓库提供）  
- `NVIDIA-Linux-x86_64-550.135.run`  
- `code-server-4.98.0-linux-amd64.tar.gz`  

## 2. 构建 Docker 镜像  
执行以下命令创建镜像（预计耗时 40 分钟，主要用于安装 PyTorch）：  
```bash
docker build -t vscode-plug-cuda:v4.98.0 .
```
**说明：**  
Dockerfile 已默认安装 **CUDA 12.4**。

## 3. 启动容器并安装插件  
### 3.1 运行容器  
```bash
docker run -d -p 0.0.0.0:31000:31000 --name=vscode vscode-plug-cuda:v4.98.0
```

### 3.2 插件安装到容器  

进入插件市场：点击左侧 扩展（Extensions） 图标，或者按 Ctrl + Shift + X。
搜索并安装插件：在搜索框输入插件名称（如 ```Cline``` 、 ```TONGYI Lingma```、```Roo Code```等插件），点击 安装 按钮，等待安装完成。

## 4. 保存和导出镜像  
### 4.1 提交修改后的容器为新镜像  
```bash
docker commit vscode vscode-plug-cuda:v4.98.0
```

### 4.2 导出镜像为 tar 包  
```bash
docker save -o vscode-plug-cuda_v4.98.0.tar vscode-plug-cuda:v4.98.0
```

## 5. 使用镜像  
在需要使用的环境中加载镜像：  
```bash
docker load -i vscode-plug-cuda_v4.98.0.tar
docker images | grep vscode
```
确认镜像加载成功后，即可使用。

本镜像使用的是code-server，如果要使用插件，那么部署的时候前端需要是https协议，否则插件几乎都无法使用仅通义灵码可用。