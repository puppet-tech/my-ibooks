---
categories:
  - 技术文章
  - 中间件
  - NGINX
---
当 NGINX 的日志文件变得过大时，通常的做法是进行日志切片（log rotation），这样可以防止单个日志文件过大，便于管理和存储。

而 NGINX 自身或者其扩展的模块均不支持类似的功能。 所以 NGINX 日志切片通常需要依赖外部工具来实现，比如 Linux 系统中常用的 `logrotate`。以下是如何使用 `logrotate` 对 NGINX 日志进行切片的步骤：

<!--more-->

## 1.1 安装 `logrotate`

如果系统中尚未安装 `logrotate`，可以通过包管理器进行安装。例如，在基于 Debian 的系统上，可以使用：

```bash
sudo apt-get install logrotate
```

## 1.2 配置 `logrotate` 为 NGINX 日志

`logrotate` 的配置通常位于 `/etc/logrotate.d/` 目录。你可以创建或修改一个配置文件专门用于 NGINX。以下是一个基本的配置示例：

```bash
sudo nano /etc/logrotate.d/nginx
```

在打开的编辑器中，添加以下内容：

```nginx
/var/log/nginx/*.log {
    daily                  # 每天轮换日志
    missingok              # 如果日志丢失不报错
    rotate 7               # 保存7个备份
    compress               # 压缩日志文件
    delaycompress          # 延迟压缩
    notifempty             # 如果是空文件则不轮换
    create 0640 nginx adm  # 创建新文件的权限
    sharedscripts
    postrotate
        if [ -f /var/run/nginx.pid ]; then
            kill -USR1 `cat /var/run/nginx.pid`
        fi
    endscript
}
```

这个配置的作用是：

- 每天对 `/var/log/nginx/*.log` 下的所有日志文件进行轮换。
- 保留 7 天的日志备份。
- 轮换后的日志进行压缩，且在下一次轮换之前不进行压缩。
- 创建新的日志文件时使用指定的权限。
- `postrotate` 脚本在完成日志轮换后发送 USR1 信号给 NGINX 主进程，这会令 NGINX 重新打开日志文件，确保日志写入不会中断。

## 1.3 测试 `logrotate` 配置

完成配置后，可以手动运行 `logrotate` 来测试配置是否有效：

```bash
sudo logrotate -v /etc/logrotate.d/nginx
```

这条命令会以详细模式运行 `logrotate`，显示轮换过程中的详细信息，有助于确认配置是否按预期执行。

## 1.4 自动执行

`logrotate` 通常由 cron 定期自动执行。你可以检查系统的 cron 任务来确认 `logrotate` 的执行频率：

```bash
cat /etc/cron.daily/logrotate
```

这样设置之后，NGINX 的日志文件就会定期自动切片和管理，减少了管理大量日志文件的复杂性和对系统性能的影响。
