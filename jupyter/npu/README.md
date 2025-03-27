# 基础镜像

加速地址是

docker.imgdb.de/ubuntu:22.04

实际地址是

ubuntu:22.04

# Jupyter + NPU 镜像构建流程  

## 0. 环境准备

复制`../npu_environment/`下的所有文件夹和文件到当前目录下，和`Dockerfile`同级

## 1. 构建 Docker 镜像  
执行以下命令创建镜像：  
```bash
docker build -t jupyter-npu:miniconda-arm .
```

## 3. 启动容器并测试  
### 3.1 运行容器  
```bash
docker run -d -p 0.0.0.0:31000:31000 --name=jupyter jupyter-npu:miniconda-arm
```

## 4. 保存和导出镜像  
### 4.1 提交修改后的容器为新镜像  
```bash
docker commit jupyter jupyter-npu:miniconda-arm
```

### 4.2 导出镜像为 tar 包  
```bash
docker save -o jupyter-plug-npu_miniconda_arm.tar jupyter-npu:miniconda-arm
```

## 5. 镜像存储位置  
已打包镜像存储在 `172.28.100.36` 服务器的 `/data1/ide` 目录下：  
```
/data1/ide/jupyter-plug-npu_miniconda_arm.tar
```

## 6. 使用镜像  
在需要使用的环境中加载镜像：  
```bash
docker load -i jupyter-plug-npu_miniconda_arm.tar
docker images | grep jupyter
```
确认镜像加载成功后，即可使用。