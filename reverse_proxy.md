### 基础镜像为 jlesage/baseimage-gui:ubuntu-22.04-v4 的容器进行反向代理指定自定义的路径访问，不使用默认的根路径/进行访问

* 通过 NGINX 反向代理，将 IDEA 插件容器的服务映射到路径 `/ide/`
* 避免直接将 IDEA 容器的端口暴露到公网
* 使用 Docker 自定义网络实现容器间通信

创建一个名为 `web-net` 的自定义网络，以便容器间通信：

```bash
docker network create web-net
```

在不暴露端口的情况下启动 IDEA 容器，并连接到 `web-net` 网络：

```bash
docker run -d \
  --name idea \
  --network web-net \
  jetbrains-idea-plug-cuda:v2024.2.5
```

在当前目录下创建 `default.conf` 文件，内容如下：

```nginx
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}

upstream idea_app {
    # 使用连通的容器的名字做反向代理
    server idea:31000;
}

server {
    listen       80;
    server_name  _;

    location = /ide {
        return 301 $scheme://$http_host/ide/;
    }

    location /ide/ {
        rewrite ^/ide(/.*)$ $1 break;

        proxy_pass http://idea_app/;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_read_timeout 86400;
    }
}
```

在当前目录下创建 `Dockerfile` 文件，内容如下：

```dockerfile
FROM nginx:alpine

COPY default.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

构建自定义 NGINX 镜像并运行容器，连接到 `web-net` 网络，并将容器的 80 端口映射到宿主机的 31000 端口：

```bash
docker build -t custom-nginx-proxy .
docker run -d \
  --name nginx-proxy \
  --network web-net \
  -p 0.0.0.0:31000:80 \
  custom-nginx-proxy
```

现在可以通过访问 `http://<宿主机IP>:31000/ide/` 来使用 JetBrains IDEA 

* 外部请求通过 NGINX 代理到 IDEA 容器
* IDEA 容器的端口未直接暴露到公网，增强了安全性
* 容器间通信通过自定义网络 `web-net` 实现
