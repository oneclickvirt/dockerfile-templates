# 基础镜像

加速地址是

docker.imgdb.de/jlesage/baseimage-gui:ubuntu-22.04-v4 

实际地址是

jlesage/baseimage-gui:ubuntu-22.04-v4

# 本地文件下载地址

| Item                              | Link                                                                                      |
|-----------------------------------|-------------------------------------------------------------------------------------------|
| eclipse-jee-2025-03-R-linux-gtk-aarch64.tar.gz | [Download Link](https://mirrors.nju.edu.cn/eclipse//technology/epp/downloads/release/2025-03/R/eclipse-jee-2025-03-R-linux-gtk-aarch64.tar.gz)   |

# Eclipse 镜像构建流程  

## 1. 下载必要文件  
在当前目录下下载以下文件：  
- `Dockerfile`（本仓库提供）  
- `eclipse-jee-2025-03-R-linux-gtk-aarch64.tar.gz`

## 2. 构建 Docker 镜像  
执行以下命令创建镜像：  
```bash
docker build -t eclipse:v2025.03-jdk17-arm64 .
```

## 3. 启动容器并测试  
### 3.1 运行容器  
```bash
docker run -d -p 0.0.0.0:31000:31000 --name=eclipse eclipse:v2025.03-jdk17-arm64
```

上去设置一些默认设置后执行后续操作

## 4. 保存和导出镜像  
### 4.1 提交修改后的容器为新镜像  
```bash
docker commit eclipse eclipse:v2025.03-jdk17-arm64
```

### 4.2 导出镜像为 tar 包  
```bash
docker save -o eclipse_v2025_03_jdk17_arm64.tar eclipse:v2025.03-jdk17-arm64
```

## 5. 使用镜像  
在需要使用的环境中加载镜像：  
```bash
docker load -i eclipse_v2025_03_jdk17_arm64.tar
docker images | grep eclipse
```
确认镜像加载成功后，即可使用。