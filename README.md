# GL-MT3600BE 满血 daede 双内核极速固件

[![Build Status](https://img.shields.io/github/actions/workflow/status/STONE0007/GL-MT3600BE-daede/build-mt3600be.yml?branch=main&style=flat-square&label=Build%20Status)](https://github.com/STONE0007/GL-MT3600BE-daede/actions)
[![Latest Release](https://img.shields.io/github/v/release/STONE0007/GL-MT3600BE-daede?style=flat-square&color=blue&label=Release)](https://github.com/STONE0007/GL-MT3600BE-daede/releases)
[![Telegram Group](https://img.shields.io/badge/Telegram-交流讨论群-2CA5E0?style=flat-square&logo=telegram&logoColor=white)](https://t.me/你的TG群组链接)
[![License: GPL-3.0](https://img.shields.io/badge/License-GPL--3.0-green.flat-square)](LICENSE)

专为 **GL.iNet GL-MT3600BE**（MediaTek Filogic 820/880 平台）深度打造的电竞级优化 OpenWrt 固件[cite: 1]。全面解锁内核级 Netkit 虚拟网卡通道与原生 eBPF 性能，集成 `dae` 与 `daed` 双完全体代理内核，并通过多层白名单机制剔除冗余运行时，实现仅约 **46MB** 的极致轻量体积与低抖动游戏转发[cite: 1, 5]。

---

## 🌟 核心特性与调优矩阵

* **双内核完全体架构**：同步集成 `dae`（极致轻量文本内核）与 `daed`（现代化图形管理面板），支持在 LuCI 界面热重载与无缝切换[cite: 1, 5]。
* **Netkit 极速模式点火**：底层彻底移除精简版 `ip-tiny`，换装完整版 `ip-full` 与 `tc-bpf`，打通 Netkit 直连转发，规避传统 veth 模式的性能损耗与高并发瓶颈[cite: 1]。
* **电竞级内核深度调优**：
  * **低延迟抢占调度**：激活 `Preemptible Kernel (Low-Latency Desktop)` 抢占模型，大幅降低网络高负载与复杂规则下的中断抖动[cite: 1]。
  * **BBR + FQ 发包起搏**：内核默认锁定 BBR 拥塞控制算法与原生 Fair Queue 队列调度，改善弱网出站吞吐与测速跑满能力[cite: 1]。
  * **XDP 快车道与零拷贝**：启用内核层 `XDP sockets`，打通数据包快速旁路通道[cite: 1]。
  * **cgroup2 与套接字监控**：启用 `CGROUP_BPF`、`INET_DIAG` 与套接字快速销毁接口，支持精准进程分流与节点秒切[cite: 1]。
* **BTF 核心说明书**：内置生成 `vmlinux-btf` 二进制字典，支持 eBPF CO-RE 机制在内核中平稳运行[cite: 1]。
* **纯净白名单防护**：在源码构建期与依赖解析期两次执行白名单过滤，彻底剥离 OpenClash 独立核心（省去 ~27MB 冗余）及 Ruby 等重型运行时，固件维持在 46MB 黄金容量[cite: 5]。
* **开箱即用调优**：集成针对 512MB 内存核算的高并发网络参数，预装 `luci-theme-aurora` 极简磨砂主题，校准 Asia/Shanghai 时区数据库[cite: 1, 5]。

---

## 📥 固件下载

所有固件均由 GitHub Actions 自动化流水线编译打包，并自动发布至 **[Releases](../../releases)** 页面供永久下载：

* **Git Tag 规范**：`vYYYY.MM.DD-HHMM`（标准日期流）
* **发布版本标题**：`GL-MT3600BE 满血 daede 固件 (YYYY-MM-DD 上午/下午)`
* **固件文件名**：`daede-YYYYMMDD-上午/下午HHMM.bin`[cite: 5]

---

## 🚀 刷机操作指南（U-Boot 网页恢复模式）

建议使用路由器底层的 U-Boot 恢复模式进行纯净刷入或跨版本升级：

1. **配置网卡静态 IP**：
   * 将网线连接至电脑与路由器的 **LAN 口**。
   * 打开 Windows 网络适配器设置，将有线网卡的 IPv4 手动指定为：
     * IP 地址：`192.168.1.2`
     * 子网掩码：`255.255.255.0`
     * 默认网关：`192.168.1.1`
2. **触发 U-Boot 恢复模式**：
   * 拔掉路由器电源线。
   * 用卡针或手指长按机身上的 **Reset 重置键** 不要松开，同时插上电源供电。
   * 持续保持按住 Reset 键约 **5~8 秒**，直到机身指示灯呈现规律的交替闪烁状态后松手。
3. **上传并刷写固件**：
   * 在电脑浏览器中访问 `http://192.168.1.1` 进入 U-Boot 网页恢复控制台。
   * 点击 **选择文件**，选中已下载的 `daede-*.bin` 固件。
   * 点击 **Update** 按钮确认烧录，等待路由器自动刷入并重启（耗时约 2~3 分钟）。
4. **重新登录系统**：
   * 将电脑网卡恢复为 **自动获取 IP (DHCP)**。
   * 浏览器访问 `http://192.168.1.1` 进入 OpenWrt 系统后台（默认无密码）。

---

## 🔍 调优验收与状态校验

刷机成功首次进入系统后，使用 SSH 登录路由器（`ssh root@192.168.1.1`），依次执行以下命令验收各项底层优化是否已生效[cite: 1]：

| 验证项目 | 检查命令[cite: 1] | 预期输出[cite: 1] | 状态判定[cite: 1] |
| :--- | :--- | :--- | :--- |
| **Netkit 驱动** | `ip link add dev test-nk type netkit`[cite: 1] | 无报错，直接返回命令提示符[cite: 1] | Netkit 极速模式通道畅通[cite: 1] |
| **BTF 核心字典** | `ls -lh /sys/kernel/btf/vmlinux`[cite: 1] | 显示文件大小在 3.5MB ~ 5MB 左右[cite: 1] | 内核字典装载成功[cite: 1] |
| **TCP 拥塞算法** | `sysctl net.ipv4.tcp_congestion_control`[cite: 1] | `= bbr`[cite: 1] | BBR 已接管网络流量[cite: 1] |
| **队列调度规则** | `sysctl net.core.default_qdisc`[cite: 1] | `= fq`[cite: 1] | 原生 FQ 队列规则生效[cite: 1] |
| **BPF JIT 状态** | `cat /proc/sys/net/core/bpf_jit_enable`[cite: 1] | `1`[cite: 1] | eBPF 即时编译引擎就绪[cite: 1] |
| **cgroup2 挂载** | `mount \| grep -E "bpf\|cgroup2"`[cite: 1] | 包含 `type bpf` 与 `type cgroup2`[cite: 1] | 进程级精准分流底层就绪[cite: 1] |
| **双内核完全体** | `dae -v && daed -v`[cite: 1] | 打印出对应内核版本号[cite: 1] | 双完全体就绪且支持热切换[cite: 1] |

*(验收完成后，可运行 `ip link delete dev test-nk` 清理临时测试接口)*

---

## 💬 交流与反馈

欢迎加入讨论群组，交流 Filogic 880 平台编译技巧、分流路由规则与电竞网络加速方案：

[![Telegram Group](https://img.shields.io/badge/Telegram-加入交流群组-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/你的TG群组链接)

---

## ☕ 赞赏支持

如果该固件在 Wi-Fi 7 跑满带宽、降低游戏延迟或提升网络稳定性方面对你有所帮助，欢迎支持持续维护：

| 微信赞赏 | 支付宝赞赏 |
| :---: | :---: |
| <img src="assets/wechat.png" width="180" alt="微信赞赏码"/> | <img src="assets/alipay.png" width="180" alt="支付宝赞赏码"/> |

> *注：请将赞赏码图片上传至仓库 `assets/` 目录，并确认命名为 `wechat.png` 和 `alipay.png`。*

---

## 📄 开源项目引用与致谢

* [OpenWrt Project](https://github.com/openwrt/openwrt)
* [KaguyaRing/openwrt-custom-gl-mt3600be](https://github.com/KaguyaRing/openwrt-custom-gl-mt3600be)[cite: 6]
* [kenzok8/openwrt-daede](https://github.com/kenzok8/openwrt-daede)[cite: 1]
* [eamonxg/luci-theme-aurora](https://github.com/eamonxg/luci-theme-aurora)[cite: 1]
* [sbwml/packages_lang_golang](https://github.com/sbwml/packages_lang_golang)[cite: 6]
