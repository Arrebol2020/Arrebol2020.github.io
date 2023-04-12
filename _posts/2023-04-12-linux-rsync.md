---
layout: post
title: 使用 rsync 实现两台 Linux 服务器之间传输文件
date: 2023-04-12 15:05 +0800
categories:
- 学习随笔
- Linux
tags:
- Linux
- rsync
---



由于之前实现两台 Linux 服务器之间传输文件都是通过压缩 A 服务文件 --> 下载 --> 上传到 B 服务器 --> 解压缩。十分愚蠢的方法，一点都不优雅，想要更优雅的方法（最近的数据文件数目太过庞大，使用该方法太费时费力），一步到胃。

   

## rsync

rsync 是一种用于在不同系统之间同步文件和目录的工具，它可以在本地或者通过网络连接同步文件。rsync 是一个非常强大的工具，可以帮助用户在多个地方同步文件、备份文件，或将文件从一台计算机复制到另一台计算机。 rsync 是 Linux/Unix 操作系统中常用的命令之一，也可以在 Windows 和 MacOS 等系统中使用。

使用 rsync 命令，需要指定源文件路径、目标路径、以及一些选项。其中，选项可以帮助用户控制同步的行为，例如是否进行压缩、是否删除目标目录中不再存在的文件等。

以下是一个简单的使用rsync的示例命令：

```bash
rsync -avz /path/to/source/ username@remotehost:/path/to/destination/
```

这个命令将会将本地的 `/path/to/source/` 目录同步到远程主机 `remotehost` 上的 `/path/to/destination/` 目录中。其中，选项 `-a` 表示进行归档同步，即保持文件属性、权限、时间等信息不变；选项 `-v` 表示显示同步的进度；选项 `-z` 表示进行压缩，可以加快数据传输速度。

除了常见的选项之外，rsync还提供了很多其他的选项，例如 `-r` 表示进行递归同步， `-u` 表示只同步更新的文件等。在使用rsync时，可以通过 `man rsync` 命令查看完整的文档和选项列表。

   

## 使用样例

同步文件：

- 将本地 a.txt 文件同步到 remotehost 的指定路径中：

  ```bash
  rsync /path/to/source/a.txt username@remotehost:/path/to/destination/b.txt
  ```

- 将 remotehost 的 a.txt 同步到本地服务器的指定路径中：

  ```bash
  rsync username@remotehost:/path/to/destination/b.txt /path/to/source/a.txt



同步文件夹：

- 将本地 src 文件夹和文件夹中的所有内容复制到 remotehost 服务器上的 /path/to/destination/ 中：

  ```bash
  rsync -vzr /path/to/source/ username@remotehost:/path/to/destination/
  ```

- 将 remotehost 服务器上的 /path/to/destination/ 文件夹及文件夹中的所有内容复制到本地 src 文件夹中：

  ```bash
  rsync -vzr username@remotehost:/path/to/destination/b.txt /path/to/source/a.txt
  ```

   

## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
