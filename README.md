[Ghost](https://ghost.org) 博客程序最近更新特别快，以前用的是 0.11.11 版本，最新版已经是 1.8.0 了，1.x 和 0.xx 的区别很是很大的。

## 运行博客的载体

本博客运行在 [Docker](https://www.docker.com/) 容器上，使用的镜像是 [ghost:1.8.0](https://store.docker.com/images/ghost)。所以本篇文章是基于 Docker 开展的。需要有一定的基础。

## 升级过程

拉取 `ghost:1.8.0` 镜像：
```
docker pull ghost:1.8.0
```

启动参数：
```
docker run -it --name blog -p 2368:2368 -v /var/lib/ghost/config.production.json:/var/lib/ghost/config.production.json -v /var/lib/ghost/content:/var/lib/ghost/content ghost:1.8.0
```

> [config.production.json](https://github.com/bubbleatgit/docker-ghost/blob/master/config.production.json) 文件要提前准备好，根据自己的实际情况修改配置文件。

## 进阶

`docker-compose` 是用来管理 docker 容器的一个好工具，我们可以用它轻松的创建、启动、重启 docker 容易。

安装 docker-compose：
```
sudo -i
curl -L https://github.com/docker/compose/releases/download/1.15.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```
> 上面 1.15.0 为 docker-compose 的版本号，可以在 github 仓库找到[版本列表](https://github.com/docker/compose/releases)

创建 docker-compose.yml 文件：
```
version: "3.2"
services:
  ghost:
    image: "ghost:1.8.0"
    volumes:
      - /var/lib/ghost/content:/var/lib/ghost/content
      - "/var/lib/ghost/config.production.json:/var/lib/ghost/config.production.json"
    ports: 
      - "2368:2368"
```

> version: "3.2" 需根据 docker-compose 版本支持的参数来改，详细内容请查看官方文档 [Compose file reference](https://docs.docker.com/compose/compose-file/)

启动容器：
```
docker-compose up -d    # 使用 docker-compose --help 获取帮助
```

## 数据备份还原

* 数据库
由于 0.xx 到 1.x 数据库结构变了，需要合并数据库，曾尝试使用官方的工具合并，出现各种问题。于是就用后台的导入导出功能来备份还原博客文章及设置的数据。

* 图片及附件

这个没什么好说的，备份原来的 images 目录还原到新版本对应目录即可。