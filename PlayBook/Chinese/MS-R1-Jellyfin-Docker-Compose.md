# MS-R1 — 在 Docker 中运行 Jellyfin

## 概述
MS-R1 基于 CIX CP8180（ARMv9，带 Arm Immortalis-G720 MC10 GPU），支持 AV1 / H.265 / H.264 硬件解码与 H.265/H.264 硬件编码。  
本 PlayBook 指导如何在该平台上以 Docker 运行带硬件编解码支持的 Jellyfin。

## 前提条件
- 已安装并配置好 Docker 与 docker-compose（可参考：How To Run Android In Docker 的“Prepare Docker environment for Android”章节）。
- 已取得由厂方或项目方提供的预构建镜像文件（jellyfin-cix.tar.gz）与 docker-compose.yml。

## 下载与文件说明

https://drive.google.com/file/d/1lq9LFVpjAbwwC1J8UR-Or9qKwkNOat2Y/view?usp=drive_link

- jellyfin-cix.tar.gz：预构建的 Jellyfin Docker 镜像（含 CP8180 的编解码库）。
- docker-compose.yml：用于编排 Jellyfin 容器及相关设备、卷映射与端口设置。

## 部署步骤

### 1. 复制文件到 MS-R1
将以下文件复制到目标主机（例如 /home/user/jellyfin 或其他目录）：
- jellyfin-cix.tar.gz
- docker-compose.yml

### 2. 导入 Docker 镜像
在目标主机上运行：
```bash
gunzip -c jellyfin-cix.tar.gz | docker load
```

### 3. 启动容器
在包含 docker-compose.yml 的目录运行：
```bash
docker compose up -d
```

## 故障排查
- 如果出现类似错误：
  Error response from daemon: error gathering device information while adding custom device "/dev/video2": no such file or directory
  - 说明 docker-compose.yml 中列出的某些设备在当前主机不存在。编辑 docker-compose.yml，移除或注释掉不存在的 /dev/* 设备项，然后重试 `docker compose up -d`。
- 若镜像加载失败，检查文件完整性与磁盘空间。

## 访问与媒体挂载
- 在客户端通过浏览器访问 Jellyfin：http://<host-ip>:8096 （例如 http://172.16.64.53:8096）
- 默认容器内媒体挂载路径：/Media（在主机上对应的路径由 docker-compose.yml 中的 volumes 指定，例如 /DATA/Media）

在 Jellyfin Web UI 中添加媒体库时，指定容器内路径 /Media 即可访问主机上的媒体文件。

## 自定义挂载
如需挂载其他目录，请编辑 docker-compose.yml 中的 volumes 部分，例如：
```yaml
volumes:
  - /DATA/Media:/Media:ro
  - /DATA/Downloads:/Downloads:ro
```
修改后重新运行：
```bash
docker compose down
docker compose up -d
```

如需进一步规范 docker-compose.yml 或添加示例配置，告知我需要的设备与路径信息，我可帮你生成或修改文件。