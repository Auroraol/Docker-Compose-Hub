<div align="center">
<h1 align="center">Docker-Compose-Hub</h1>
</div>
# 项目的目录结构

* docs/dev-environment/ - 基础开发环境（MySQL、Redis、RabbitMQ等）
* docs/tools/ - 常用工具（密码管理器、博客、监控工具等）
* template/ - 文档模板
* .github/ - GitHub Actions 配置
* .husky/ - Git hooks 配置

# 部署 Docker

## 安装Docker

**官方的一键安装方式**

```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

**启动Docker并测试**

```shell
# 启动Docker服务
systemctl start docker
# 设置开机自动启动
systemctl enable docker
# 测试
docker ps
```

![image-20231002194321227](https://github.com/Auroraol/Drawing-bed/raw/main/img/image-20231002194321227.png)

## 安装DockerCompose

注意: 新版docker直接使用`docker compose`命令,无需安装`docker-compose`

```shell
# 安装DockerCompose
curl -SL https://github.com/docker/compose/releases/download/v2.27.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

# 分配权限
sudo chmod +x /usr/local/bin/docker-compose

# 查看版本
docker-compose --version
```

![image-20240430223322693](https://github.com/Auroraol/Drawing-bed/raw/main/img/image-20240430223322693.png)

# 使用 Docker-Compose-Hub

以mysql为例

```bash
# 1
git clone https://github.com/MoshiCoCo/docker-compose-hub.git

# 2
mkdir -p <your_target_directory>/docker-mysql8
sudo cp docker-compose-hub/docs/dev-environment/mysql/docker-compose.yml <your_target_directory>/docker-mysql8

# 3 修改配置
vim docker-compose.yml  # 修改自定义配置(默认挂载目录为当前目录)

# 4 构建容器
docker compose up --build -d # 或者 docker-compose up --build -d
```

- 如果在开发环境中且经常修改 `Dockerfile` 或应用代码，请使用 `docker compose up --build -d`。
- 如果镜像已经构建完成且未发生变化，或者是在生产环境中，请使用 `docker compose up -d`。

![image-20250608165712995](https://github.com/Auroraol/Drawing-bed/raw/main/img/image-20250608165712995.png)

```shell
# 进入容器 
docker exec -it 容器id(或者容器的名称) /bin/bash

# 直接进入 MySQL 命令行客户端
docker exec -it 容器id(或者容器的名称) mysql -u root -p

#退出容器
exit # 或者 ctrl + D
```

直接进入 MySQL 命令行客户端

![image-20250608170525031](D:/Github/docker-compose-hub/README.assets/image-20250608170525031.png)

先进入容器再进入MySQL命令行客户端

![image-20250608170737425](https://github.com/Auroraol/Drawing-bed/raw/main/img/image-20250608170737425.png)

# 约定

**1.在 docker-compose.yml 配置文件中，有些参数是可变的**

以redis镜像的docker-compose编排文件为例:

```yaml
version: "3"
services:
  redis:
    image: redis
    restart: always
    container_name: redis-6000
    volumes:
      - $PWD/data:/data
      - $PWD/logs:/logs
    command: redis-server --requirepass <your-redis-connaction-password>
    ports:
      - "6000:6379"
```

**2.密码相关**
本项目中此类均以`< some user password or username >`的形式展示。如果你的密码是 `lfj74`  正确写法：`command: redis-server --requirepass lfj74`

# 目前支持的镜像列表

## 基础开发环境

此处的容器为单个容器，按需求启动。

- [Portainer](docs/dev-environment/portainer/server)  docker容器管理工具，包括server和edgeagent
- [MySQL](docs/dev-environment/mysql)
- [Redis](docs/dev-environment/redis)
- [Redis 集群（3 主 3 从）](docs/dev-environment/redis-cluster)
- [Rabbit MQ](docs/dev-environment/rabbit-mq)
- [Nginx Consul Registrator](docs/dev-environment/nginx-consul-registrator)
- [MinIO](docs/dev-environment/minio)
- Nacos
- ShardingSphere-Proxy
- .....

## 常用工具

- [VaultWarden](docs/tools/vaultwarden) 密码管理器
- [QingLong](docs/tools/qinglong) 脚本运行服务
- [Bark Server](docs/tools/bark) 消息推送服务端
- [DrawIo](docs/tools/drawio) 画图工具，流程图，UML 等
- [Acme.sh](docs/tools/acme-sh) 免费 SSL 生成工具
- [Halo](docs/tools/halo) Halo 博客
- [Frp](docs/tools/frp) frp 代理服务器
- [NetData](docs/tools/netdata) Netdata 网络监控工具
- [WordPress](docs/tools/wordpress) WordPress博客
- .....

# 参与贡献

```bash
npm intsll && npm install -g pnpm && pnpm install
```

请按照模板文件`/template`的格式填写，并将文件放置到 `docs/` 目录下对应的类别中

格式检查

```bash
pnpm lint
#或者npm run lint
```

格式检查实现原理

```
pnpm lint 或 npm run lint 的格式检查是通过以下方式实现的：
1. 工具：使用 markdownlint-cli2 工具进行 Markdown 文件的格式检查
2. 配置：在 package.json 中定义了 lint 脚本
3. 规则配置：通过 .markdownlint.json 文件配置检查规则：
* 禁用了一些规则（MD009、MD013、MD012、MD033、MD041）
* 允许 HTML 元素
* 配置了行长度检查
1. 自动化：通过 husky 配置了 Git hooks，在提交代码时自动运行格式检查
```

# 参考

[MoshiCoCo/docker-compose-hub:](https://github.com/MoshiCoCo/docker-compose-hub/tree/main)
