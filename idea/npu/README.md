# 基础镜像

jlesage/baseimage-gui:ubuntu-22.04-v4

# 本地文件下载地址

| Item                              | Link                                                                                      |
|-----------------------------------|-------------------------------------------------------------------------------------------|
| ideaIC-2024.2.1-aarch64.tar.gz | [Download Link](https://download.jetbrains.com/idea/ideaIC-2024.2.1-aarch64.tar.gz)   |
| continue-intellij-extension-0.0.56.zip        | [Plugin Download Page](https://plugins.jetbrains.com/plugin/22707-continue/versions/stable)         |
| CodeGPT-2.16.4-241.1.zip         | [Plugin Download Page](https://plugins.jetbrains.com/plugin/21056-proxy-ai)                         |

# JetBrains idea + NPU + AI插件 镜像构建流程  

## 1. 下载必要文件  
在当前目录下下载以下文件：  
- `Dockerfile`（本仓库提供）  
- `ideaIC-2024.2.1-aarch64.tar.gz`  

同时复制`../npu_environment/`下的所有文件夹和文件到当前目录下，和`Dockerfile`同级

## 2. 构建 Docker 镜像  
执行以下命令创建镜像：  
```bash
docker build -t jetbrains-idea-plug-npu:v2024.2.1-arm .
```

## 3. 启动容器并安装插件  
### 3.1 运行容器  
```bash
docker run -d -p 0.0.0.0:31000:31000 --name=idea jetbrains-idea-plug-npu:v2024.2.1-arm
```

### 3.2 复制插件安装包到容器  
```bash
docker cp CodeGPT-2.16.4-241.1.zip idea:/home
docker cp continue-intellij-extension-0.0.56.zip idea:/home
```

### 3.3 通过浏览器安装插件  
1. 在浏览器中打开 `http://<宿主机IP>:31000/`，进入在线 idea。  
2. **首次运行设置**：  
   - 勾选 **接受用户协议**，点击 `Continue` 按钮。  
   - 选择 **不发送错误数据**，点击 `Don't send` 按钮。  
3. **安装插件**：  
   - 进入 `Plugins` 选项卡。  
   - 点击右上角 **齿轮按钮**，选择 `Install plugin from local disk`。  
   - 在弹出的窗口中输入路径 `/home` 并回车，选择对应的 `zip` 插件包进行安装。  
   - 安装过程中如果提示 **是否接受未知来源的插件**，点击 **确认** 继续安装。  
4. **验证插件是否成功安装**：  
   - 在 `/home/ubuntu` 目录下创建新项目并信任打开。  
   - 右侧边栏应该出现插件的图标，表示安装成功。  

### 3.4 重启容器检查插件持久性  
```bash
docker restart idea
```
刷新浏览器，重新进入项目，检查插件是否仍然存在。  

### 3.5 清理无用安装包（减少镜像大小）  
通过 idea 内置终端进入 `/home` 目录，删除 `zip` 安装包。

## 4. 保存和导出镜像  
### 4.1 提交修改后的容器为新镜像  
```bash
docker commit idea jetbrains-idea-plug-npu:v2024.2.1-arm
```

### 4.2 导出镜像为 tar 包  
```bash
docker save -o jetbrains-idea-plug-npu_v2024.2.1_arm.tar jetbrains-idea-plug-npu:v2024.2.1-arm
```

## 5. 使用镜像  
在需要使用的环境中加载镜像：  
```bash
docker load -i jetbrains-idea-plug-npu_v2024.2.1_arm.tar
docker images | grep jetbrains-idea
```
确认镜像加载成功后，即可使用。