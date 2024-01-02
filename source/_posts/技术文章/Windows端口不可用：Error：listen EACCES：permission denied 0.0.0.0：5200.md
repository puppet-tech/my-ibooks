---
categories:
  - 技术文章
---
![image.png](https://oss.puppetdev.top/image/note/2f2010b43ba763c7c96a14e1fd1afe3d.png)

当我使用 Windows 11 下的 VSCode 启动 Vue 项目时，出现以下报错：

```
ERROR  error when starting dev server:                                        
Error: listen EACCES: permission denied 0.0.0.0:5200
    at Server.setupListenHandle [as _listen2] (node:net:1800:21)
    at listenInCluster (node:net:1865:12)
    at doListen (node:net:2014:7)
    at process.processTicksAndRejections (node:internal/process/task_queues:83:21)

 ELIFECYCLE  Command failed with exit code 1
```

<!--more-->

## 1.1 环境说明

- VSCode：

```
    Version: 1.85.1 (user setup)
    Commit: 0ee08df0cf4527e40edc9aa28f4b5bd38bbff2b2
    Date: 2023-12-13T09:49:37.021Z
    Electron: 25.9.7
    ElectronBuildId: 25551756
    Chromium: 114.0.5735.289
    Node.js: 18.15.0
    V8: 11.4.183.29-electron.0
    OS: Windows_NT x64 10.0.22621
```

- Windows 11

```
    版本	Windows 11 专业版
    版本	22H2
    安装日期	‎2022-‎10-‎07
    操作系统版本	22621.2861
```

## 1.2 解决方案

在 Windows 中，某些端口范围可能被系统保留用于特定服务，例如动态端口分配给 Hyper-V 虚拟机、Windows 更新服务、或其他网络服务。**这个分配的行为是动态、随机的，所以会出现用的好好的，但突然不可用的情况**。

我们可以通过以下命令查看哪些端口被动态分配了：

```shell
# 在 cmd 窗口中执行
netsh interface ipv4 show excludedportrange protocol=tcp
```

![image.png](https://oss.puppetdev.top/image/note/addbb4e2a5c2a09278b26d060e7345e3.png)

该命令会列出 TCP 协议下被排除的、不能被使用的端口范围。

> `netsh`（网络壳层）是 Windows 提供的一个脚本化的命令行工具，用于本地或远程配置网络设备。
>
> 这个命令在网络故障排除中非常有用，特别是当我们遇到如下情况时：
>
> - 试图启动服务或监听特定端口时出现问题。
> - 确认系统是否有端口范围被 Hyper-V、Windows 更新或其他服务预留。
> - 解决端口冲突问题。
>
> 通常很难直接确定这些端口范围被保留的具体原因，因为 netsh 不提供这些信息。

知道哪些端口无法使用后，通过 `vite --port [avaiable_port]` 指定可用端口重启即可，例如：`vite --port 8080`

### 1.2.1 排除指定网段

除了上面指定可用的端口外，我们还可以将某个指定网段排除，使得该网段不会被系统动态分配，这样我们就不用担心我们要用的端口不可用。

使用 `netsh` 命令排除端口范围

1. 打开命令提示符（以管理员身份）。
2. 使用 `netsh` 命令设置动态端口范围。例如，如果想保留 `5000-5100` 范围的端口，可以先查看当前的动态端口范围：

    ```shell
    netsh int ipv4 show dynamicport tcp 
    netsh int ipv4 show dynamicport udp
    ```

3. 设置新的动态端口范围，排除想保留的端口。例如，一般 tcp 动态端口默认范围是 `1024-15000`，而 UDP 动态端口默认范围是 `49152-65535`，我们可以调整 tcp 的动态端口范围为 `35175-49151`

    ```
    netsh int ipv4 set dynamicport tcp start=35175 num=13977
    ```

    这里，`num` 参数是端口数量，计算方式为 `end_port - start_port + 1`。确保新的范围覆盖了原来的范围，同时绕过了想保留的端口。

4. 重新启动计算机以应用更改。
