---
title: 解决方案：e1000e eno1 Detected Hardware Unit Hang
date: 2024-10-31 11:27:47
mtime: 2024-11-04 20:22:28
tags:
  - Proxmox
  - Linux
  - 英特尔网卡
---

![fjy89](https://oss.puppetdev.top/image/note/8a813c25c40bdf839d7466c7a2da9098.png)

在 Proxmox 6.5.11-8 中，偶发性会出现以下报错，尤其是在进行大文件传输后：

```bash
[97377.240263] e1000e 0000:00:1f eno1: Detected Hardware Unit Hang:
                 TDH                  <22>
                 TDT                  <2f>
                 next_to_use          <2f>
                 next_to_clean        <21>
               buffer_info[next_to_clean]:
                 time_stamp           <101725292>
                 next_to_watch        <22>
                 jiffies              <1017253e0>
                 next_to_watch.status <0>
               MAC Status             <40080083>
               PHY Status             <796d>
               PHY 1000BASE-T Status  <3800>
               PHY Extended Status    <3000>
               PCI Status             <10>
```

**一旦出现该问题，Synology NAS 就会进入不可用的状态。**

## 1.1 Intel 网卡驱动问题，特别是 e1000e

错误信息表明网络接口卡（NIC）在进行数据传输时遇到了硬件单元挂起的问题。这种情况在**重网络负载**时更容易发生。

这是多年来 I219-V、I218-V… 网卡型号及其驱动程序的一个已知错误。不仅在 proxmox 上，在 xcp-ng 上也是如此。

这个问题，截止 Proxmox 6.5.11-8 仍未被彻底解决，只能通过禁用 TCP 校验和卸载。

## 1.2 解决方案

通过执行命令：`ethtool -K eno1 tso off gso off gro off` 即可解决，但是每次重启机器或者网卡，都会失效。具体含义下方会说明。

如何长期解决？

在 `/etc/network/interfaces` 中，添加以下代码：

```sh
iface eno1 inet manual
    post-up /usr/bin/logger -p debug -t ifup "Disabling offload for eno1" && /sbin/ethtool -K $IFACE tso off gso off gro off && /usr/bin/logger -p debug -t ifup "Disabled offload for eno1"
```

- **post-up**：这是一个钩子命令，它会在接口被成功激活（“up”状态）后执行。这里的命令会在 `eno1` 接口启动之后执行一系列动作。
- **/usr/bin/logger -p debug -t ifup “Disabling offload for eno1”**：这条命令使用 `logger` 程序，将一条调试级别（`debug`）的信息写入系统日志。日志内容为 `"Disabling offload for eno1"`，标记为 `ifup`。这样做的目的是记录接口启动时禁用某些功能的动作，方便日后查看系统日志，了解配置是否按预期执行。（可通过 `journalctl -t ifup -p debug` 查看日志）
- **/sbin/ethtool -K $IFACE tso off gso off gro off tx off rx off**：这里使用 `ethtool` 工具来关闭网络接口的一些“硬件卸载（offload）”功能。具体地：
    - **tso（TCP Segmentation Offload）**：关闭 TCP 分段卸载。TSO 允许网卡将大型 TCP 数据包分段，减轻 CPU 负担。如果关闭，系统会由 CPU 进行 TCP 分段。
    - **gso（Generic Segmentation Offload）**：关闭通用分段卸载。GSO 是一种将不同协议的数据分段处理的技术，关闭它会让系统自己处理分段。
    - **gro（Generic Receive Offload）**：关闭通用接收卸载。GRO 是一种接收数据包处理优化技术。关闭它意味着数据包接收将完全交由 CPU 处理。
    - **tx（Transmit Checksum Offload）**：完全关闭传输时的校验和卸载。网卡不会自动处理数据包的校验和，而是让系统自行处理。
    - **rx（Receive Checksum Offload）**：完全关闭接收时的校验和卸载，网卡不会校验接收到的数据包，需要系统来进行检查。

> [!info] 信息
> - **TSO**、**GSO** 和 **GRO** 分别用于控制 TCP 分段、通用分段和接收卸载，这三个是针对传输层和网络层的优化。
> - **tx off** 和 **rx off** 是更通用的选项，用于关闭整个发送（TX）和接收（RX）的卸载功能。
>
> 如果已经关闭了 TSO、GSO 和 GRO，那么 `tx off` 和 `rx off` 可能会显得多余，因为这些设置会影响整个数据包的处理，而不只是特定的卸载功能。
>
> 在大多数情况下，只需关闭 TSO、GSO 和 GRO 就可以了，`tx off` 和 `rx off` 可以根据需要选择是否使用。如果需要彻底禁用卸载功能，可以使用它们，但通常只关闭 TSO、GSO 和 GRO 是足够的。

> [!note] 注意
> 这对于一些系统可能没有生效，那么可以尝试通过注册 Service 的方式执行。这里不赘述。

如何查看 tso、gso、gro 是否已经关闭：`ethtool -k eno1 | grep -E 'tso|gso|gro'`

![yp93w](https://oss.puppetdev.top/image/note/9ebac05995653141eb5529a53e633a8b.png)

均为 off，说明已经关闭。

如何查看 TCP 校验和卸载是否开启：`ethtool -a eno1`

![qegq7](https://oss.puppetdev.top/image/note/a6de0c3547bf2b8d8868b0885dd1f2e1.png)

些参数主要用于控制网络接口的流量控制，以减少数据包丢失，确保在网络拥堵时网络的稳定性。

- **Autonegotiate**：开启表示接口支持自动协商功能，允许网络接口和连接到的设备协商最佳的传输模式（如全双工或半双工）和流量控制。
- **RX（Receive）**：此项“on”表示接收暂停帧的能力已开启。也就是说，网络接口在接收到暂停帧时会暂停接收数据。这在数据接收方无法及时处理流量时非常有用。
- **TX（Transmit）**：此项“on”表示发送暂停帧的能力已开启。网络接口可以在需要的时候发送暂停帧，指示对方暂停发送数据。这在本设备负载过高时很有帮助。
- **RX negotiated**：表示接收暂停帧的能力已协商开启，表明两端设备在连接时已协商启用了接收流量控制。
- **TX negotiated**：表示发送暂停帧的能力已协商开启，表明两端设备在连接时已协商启用了发送流量控制。

配置后，Synology NAS 终于不会再“突发恶疾”了！！！

## 1.3 参考文献

- [e1000e eno1: Detected Hardware Unit Hang: | Proxmox Support Forum](https://forum.proxmox.com/threads/e1000e-eno1-detected-hardware-unit-hang.59928/ "e1000e eno1: Detected Hardware Unit Hang: | Proxmox Support Forum")
- [e1000 driver hang | Page 8 | Proxmox Support Forum](https://forum.proxmox.com/threads/e1000-driver-hang.58284/page-8#post-390709 "e1000 driver hang | Page 8 | Proxmox Support Forum")
