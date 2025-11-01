# MS-R1 操作系统重装指南

## 简介

MS-R1 采用标准的 UEFI BIOS，可以引导任何兼容 UEFI 的系统 ISO。CPU 驱动程序尚未完全合并到主流 Linux 内核中，MINISFORUM 的官方系统镜像可以提供最佳的用户体验。

> **注意**：后续驱动程序合并到主流 Linux 内核中后。就可以像普通 x86 PC 一样使用任何操作系统。

## 系统要求

- 最小 40GB 存储空间
- USB 驱动器或 SSD 用于系统安装
- 用于下载系统镜像的网络连接

## 安装步骤

### 1. 下载系统镜像

最新版本：`MS-R1-2025101202-0003301.7z`

下载地址：[Google Drive 链接](https://drive.google.com/drive/folders/1FzjO50RYqKi-BIOYH2wduRYeeju-MRIU?usp=drive_link)

### 2. 将镜像写入系统磁盘

#### Windows 用户
1. 下载并安装 `balenaEtcher`
2. 解压系统镜像
3. 使用 `balenaEtcher` 将镜像写入系统磁盘

#### Linux 用户
1. 解压系统镜像
2. 使用 `dd` 命令将镜像写入系统磁盘

### 3. 系统访问

默认凭据：
- 用户名：`mini`
- 密码：`mini`

## 其他资源

### 开源代码
系统源代码可在 GitHub 上获取：[minisforum-cix-p1-repo](https://github.com/orgs/minisforum-cix-p1-repo)

### 相关文档
- [MS-R1 基础指南](../PlayBook/MS-R1-BaseGuide.md)
- [PVE 安装指南](../PlayBook/MS-R1-How-To-Install-PVE.md)
- [在 Docker 中运行 Android](../PlayBook/MS-R1-How-To-Run-Android-In-Docker.md)

> **警告**  
> 在进行系统安装之前，请确保已备份所有重要数据。



