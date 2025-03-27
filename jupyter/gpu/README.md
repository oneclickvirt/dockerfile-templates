# 基础镜像

加速地址是

docker.imgdb.de/ubuntu:22.04

实际地址是

ubuntu:22.04

# 本地文件下载地址

| Item                              | Link                                                                                      |
|-----------------------------------|-------------------------------------------------------------------------------------------|
| NVIDIA-Linux-x86_64-550.135.run | [Download Link](https://cn.download.nvidia.com/XFree86/Linux-x86_64/550.135/NVIDIA-Linux-x86_64-550.135.run)  |

# Jupyter + CUDA + AI插件 镜像构建流程  

## 1. 下载必要文件  
在当前目录下下载以下文件：  
- `Dockerfile`（本仓库提供）  
- `NVIDIA-Linux-x86_64-550.135.run`  

## 2. 构建 Docker 镜像  
执行以下命令创建镜像：  
```bash
docker build -t jupyter-plug-cuda:miniconda-amd64 .
```

## 3. 启动容器并测试
### 3.1 运行容器  
```bash
docker run -d -p 0.0.0.0:31000:31000 --name=jupyter jupyter-plug-cuda:miniconda-amd64
```


## 4. 保存和导出镜像  
### 4.1 提交修改后的容器为新镜像  
```bash
docker commit jupyter jupyter-plug-cuda:miniconda-amd64
```

### 4.2 导出镜像为 tar 包  
```bash
docker save -o jupyter-plug-cuda_miniconda-amd64.tar jupyter-plug-cuda:miniconda-amd64
```

## 5. 使用镜像  
在需要使用的环境中加载镜像：  
```bash
docker load -i jupyter-plug-cuda_miniconda-amd64.tar
docker images | grep jupyter
```
确认镜像加载成功后，即可使用。