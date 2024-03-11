---
tags: 
title: OMV结合NextCloud实现私人网盘
---

![OMV NAS & NextCloud搭建私人网盘.webp](https://oss.puppetdev.top/image/note/ab9bff5d9f1b185ad091b0387e139630.webp)

OpenMediaVault (OMV) 是一款基于 Debian 的开源网络附加存储（NAS）解决方案，它通过易于使用的界面提供数据存储、备份和共享服务。结合 Nextcloud，用户可以在 OMV 平台上搭建一个功能强大的私人网盘，实现文件同步、分享和协作。这种组合不仅增强了数据的可访问性和安全性，也为个人和小型企业提供了一个成本效益高、隐私保护好的云存储解决方案。通过 Docker 容器或直接安装，OMV 与 Nextcloud 的整合可以轻松完成，满足现代数字生活的存储需求。

<!--more-->

## 1.1 安装脚本

```shell
sudo wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash
```

该脚本会安装 openmediavault、omv-extras、flashmemory，如果已经安装好了的，就自动跳过。

### 1.1.1 flashmemory

在 OMV 系统中，`flashmemory` 插件的主要作用是减少对系统运行的存储介质（尤其是基于闪存的存储介质，如 SSD 或 USB 闪存驱动器）的写入操作。这个插件非常重要，特别是当 OMV 安装在闪存媒介上时，比如 USB 棒或 SD 卡。

它的作用：

1. **减少写入操作**：通过减少对闪存的写入次数，可以显著延长闪存设备的使用寿命。闪存设备（如 SSD、USB 闪存盘和 SD 卡）有有限的写入次数，过多的写入操作会减少它们的寿命。
2. **改善系统性能**：通过减少不必要的写入操作，可以提高系统的响应速度和整体性能。
3. **使用 tmpfs**：插件工作原理是将一些经常被写入的目录挂载到内存中（使用 tmpfs），这样这些目录下的数据写入操作实际上是在内存中进行，而不是在实际的物理存储上。系统重启时，这些写入的数据会丢失，因为它们只是临时存储在内存中。

总结：

- 如果 OMV 安装在硬盘驱动器（HDD）上，并且不担心写入次数，不一定要安装 `flashmemory` 插件
- 对于安装在闪存媒介（如 SSD、USB 闪存盘和 SD 卡）的 OMV 系统，强烈建议安装 `flashmemory` 插件。这样可以保护存储介质，延长其使用寿命，并提高系统的整体稳定性和性能。

## 1.2 创建共享文件夹用于存放 NextCloud 的 Data

1. 创建共享文件夹，记录绝对路径：/srv/dev-disk-by-uuid-3f0211f1-75af-4278-9d80-21f507bb8650/NextCloudData
    ![image.png](https://oss.puppetdev.top/image/note/b07d48b9dbffd4fd9bf424d300a886d7.png)
2. **重要**！！！修改共享文件夹的 ACL 权限，将用户组修改为 www-data，否则初始化 NextCloud 时，会出现权限不足而失败。

    ![image.png](https://oss.puppetdev.top/image/note/a322e5eab62d1dd79f525aa219685254.png)

## 1.3 借助 Compose 整体启用安装 MySQL 和 NextCloud

```yml
# 该文件最初是为了给 OMV NAS 集成 NextCloud 而写
# 已经过完整测试，确定可用，因此非必要，不建议更换镜像的版本。
version: '3'
services:
  mysql:
    container_name: mysql
    image: mysql:8.3.0
    privileged: true
    restart: unless-stopped
    environment:
      TZ: Asia/Shanghai
      # MySQL Root用户密码
      MYSQL_ROOT_PASSWORD: xxx
      MYSQL_ALLOW_EMPTY_PASSWORD: 'no'
      # 初始化数据库
      MYSQL_DATABASE: nextcloud
      # 初始化用户(不能是root 会报错)
      MYSQL_USER: nextcloud
      # 用户密码
      MYSQL_PASSWORD: MYSQL_PASSWORD
    ports:
      - '3306:3306'
      # 不需要主从，所以不需要33060
      # - '33060:33060'
    volumes:
      - /app/data/mysql/data:/var/lib/mysql:rw
      - /app/data/mysql/logs:/logs:rw
      # 下面两个映射为可选
      - /app/data/mysql/config:/etc/mysql/conf.d:ro
      - /app/data/mysql/init:/docker-entrypoint-initdb.d:ro
    logging:
      driver: 'json-file'
      options:
        max-size: '1024M'
        max-file: '5'
    networks:
      - nextcloud
  nextcloud:
    container_name: nextcloud
    image: nextcloud:28.0.3 # 该镜像内部使用了 Apache 作为 Web 代理服务器
    restart: unless-stopped
    ports:
      - '8080:80'
    volumes:
      - /app/data/nextcloud/custom_apps:/var/www/html/custom_apps
      - /app/data/nextcloud/config:/var/www/html/config
      - /app/data/nextcloud/theme:/var/www/html/themes
      - /srv/dev-disk-by-uuid-3f0211f1-75af-4278-9d80-21f507bb8650/NextCloudData:/var/www/html/data # OMV共享文件夹的绝对路径，存放实际文件数据
    environment:
      - MYSQL_PASSWORD=MYSQL_PASSWORD # 同上面 MySQL 服务的用户密码一致
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=mysql # 同一个 DockerCompose 下，Host只需要使用服务名即可
    networks:
      - nextcloud

networks:
  nextcloud:
    name: 'nextcloud'
    driver: 'bridge'
```

### 1.3.1 MySQL 与 NextCloud 配置参数说明

> 仅列举必要且常见的，更多配置说明见官方说明。

[NextCloud](https://hub.docker.com/_/nextcloud)：`https://hub.docker.com/_/nextcloud`

Nextcloud 安装以及数据库之外的所有数据（文件上载等）都存储在容器地址/var/www/html 中，要想持久化你的数据，不通过 nextCloud 也可以查看的话，应当映射到宿主机的某个位置上。

nextcloud 的一些卷地址：

- /var/www/html 更新所需的主文件夹
- /var/www/html/custom_apps 你自己手动安装的应用位置
- /var/www/html/config 本地配置文件位置
- /var/www/html/data 你的网盘数据存放的位置
- /var/www/html/themes/ 主题文件位置

[MySQL](https://hub.docker.com/_/mysql)：`https://hub.docker.com/_/mysql`

## 1.4 最后：启动 NextCloud

1. 直接用 DockerCompose 启用容器服务
2. 启动后，Web 访问 ip:8080，即可访问 NextCloud
3. 创建管理用户
4. 安装额外的应用

最后的效果：Amazing！！！！

![image.png](https://oss.puppetdev.top/image/note/9d8abde41a2ce10acd9c1ffb8e9973fa.png)

![image.png](https://oss.puppetdev.top/image/note/69da14431cb380184c83876467f6af67.png)

![image.png](https://oss.puppetdev.top/image/note/128b011a00a09c0e3f7e8e68594dd0a3.png)

## 1.5 补充：配置 SSL 证书

由于使用的是 nextcloud，而不是 fpm，因此 Web 代理采用了内置的 Apache2

- 在 apache 安装目录下新建一个文件夹 cert：`/etc/apache2/certs`
- 上传 Apache 类型的证书文件到 certs 目录下
- 启用 SSL 模块和默认的 SSL 站点配置

    ```shell
    a2enmod ssl
    a2ensite default-ssl
    ```

- 修改 default-ssl.conf 配置文件：`vim /etc/apache2/sites-available/default-ssl.conf`

    添加或修改如下语句

    ```
    SSLEngine on
    SSLCertificateFile /etc/apache2/certs/public.crt
    SSLCertificateKeyFile /etc/apache2/certs/web.key
    SSLProtocol all -SSLv2 -SSLv3
    SSLCipherSuite HIGH:!RC4:!MD5:!aNULL:!eNULL:!NULL:!DH:!EDH:!EXP:+MEDIUM
    ```

    使用 sed 命令直接替换（如果不想下载 vim 的话），**替换前记得备份**

    ```shell
    sed -i '/SSLCertificateFile      \/etc\/ssl\/certs\/ssl-cert-snakeoil.pem/,/SSLCertificateKeyFile   \/etc\/ssl\/private\/ssl-cert-snakeoil.key/c\        SSLCertificateFile /etc/apache2/certs/public.crt\n        SSLCertificateKeyFile /etc/apache2/certs/web.key\n        SSLProtocol all -SSLv2 -SSLv3\n        SSLCipherSuite HIGH:!RC4:!MD5:!aNULL:!eNULL:!NULL:!DH:!EDH:!EXP:+MEDIUM'  /etc/apache2/sites-available/default-ssl.conf
    ```

> `SSLProtocol all -SSLv2 -SSLv3`：指定 Apache 服务器支持的 SSL 和 TLS 协议版本。启用了所有可用的协议（`all`），但禁用了旧版的 `SSLv2` 和 `SSLv3`。这是因为 `SSLv2` 和 `SSLv3` 都存在严重的安全漏洞，如 POODLE 攻击，因此在现代 Web 服务器配置中应当被禁用以保证安全。

> `SSLCipherSuite HIGH:!RC4:!MD5:!aNULL:!eNULL:!NULL:!DH:!EDH:!EXP:+MEDIUM`
> ：定义 Apache 服务器在 SSL/TLS 握手过程中可以使用的加密套件（cipher suites）
> - `HIGH`：启用被认为是高强度的加密套件。
> - `!RC4`：禁用 RC4 加密算法。RC4 已被发现存在多种安全问题。
> - `!MD5`：禁用 MD5 散列函数。MD5 因其安全性不足而不推荐使用。
> - `!aNULL`：禁用允许匿名身份验证的加密套件。
> - `!eNULL`：禁用不提供加密的套件（即明文通信）。
> - `!NULL`：确保所有加密套件都必须提供消息认证。
> - `!DH`、`!EDH`：禁用不安全的 Diffie-Hellman 密钥交换方法。
> - `!EXP`：禁用出口级（被认为弱加密）的加密套件。
> - `+MEDIUM`：启用中等强度的加密套件。

> 有些还需要配置了：`SSLCertificateChainFile /etc/apache2/cert/chain.crt`
> 指定包含证书链的文件的路径。证书链文件（有时称为 CA 证书束）包含了从服务器证书到根 CA 证书（包括中间 CA 证书）的完整路径。这是必要的，因为客户端（如 Web 浏览器）需要这个证书链来验证服务器证书的真实性。Apache 2.4.8 及更高版本中推荐使用 `SSLCertificateFile` 指令来代替 `SSLCertificateChainFile`，因为 `SSLCertificateFile` 现在可以同时处理服务器证书和链证书。

- 进一步修改配置文件：`/etc/apache2/sites-available/default-ssl.conf`

    ```shell
    # 将以下内容
    ServerAdmin webmaster@localhost
    # 替换为
    ServerAdmin 邮箱
    ServerName 域名

    # 用 sed 命令替换
    sed -i '/ServerAdmin webmaster@localhost/{s/^\(\s*\)ServerAdmin webmaster@localhost/\1ServerAdmin xxx@163.com\n\1ServerName nextcloud.xxx.xxx/}' /etc/apache2/sites-available/default-ssl.conf
    ```

- 替换后可以用 `apache2ctl configtest` 检查一下配置是否有异常。
- 重启 apache 服务：`/etc/init.d/apache2 restart` or `service apache2 restart`

使用域名访问 nextcloud 即可。如果未正确识别，可清除浏览记录，关闭浏览器再重新打开。
