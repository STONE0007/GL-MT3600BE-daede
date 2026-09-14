# GL-MT3600BE 满血 daede 双内核极速固件

专为 **GL.iNet GL-MT3600BE**（MediaTek Filogic 820/880 平台）深度定制的 OpenWrt 极速固件。全面解锁 Netkit 虚拟网卡与原生 eBPF 性能，集成 `dae` 与 `daed` 双完全体代理内核，并通过多层白名单机制剔除冗余依赖，实现仅约 **46MB** 的轻量体积与极低游戏网络抖动。

---

## 🌟 核心特性与底层调优

* **双内核完全体共存**：同步集成 `dae`（极致轻量纯文本内核）与 `daed`（现代化图形控制面板），支持在 LuCI 界面热重载与无缝切换。


* **Netkit 极速模式点火**：底层剔除精简版 `ip-tiny`，换装完整版 `ip-full` 与 `tc-bpf`，直通虚拟网卡转发通道，规避传统模式性能衰减与延迟损耗。


* **内核深度调优**：
* **低延迟抢占模型**：开启 `Preemptible Kernel (Low-Latency Desktop)`，降低网络高负载与复杂路由下的中断延迟。


* **BBR + FQ 发包起搏**：内核默认锁定 BBR 拥塞控制算法与原生 Fair Queue 调度，强化弱网单连接测速与出站吞吐。


* **XDP 快车道**：激活内核层 `XDP sockets` 零拷贝网络路径。


* **cgroup2 与套接字监控**：启用 `CGROUP_BPF`、`INET_DIAG` 与连接强制回收接口，确保精准进程分流与无缝节点切换。




* **BTF 核心说明书**：完整装载 `vmlinux-btf` 二进制字典，支持 eBPF CO-RE 机制安全运行。


* **精简纯净防护**：白名单严格拦截无用组件，剔除重型语言运行时与冗余 OpenClash Core，确保固件身材保持在 ~46MB 黄金容量。


* **现代视觉体验**：预置精简版 `luci-theme-aurora` 极简主题及配套配置面板。



---

## 📥 固件获取

访问本仓库右侧的 **[Releases](https://www.google.com/search?q=../../releases)** 页面，即可直接下载最新编译构建的固件二进制文件：

* **文件命名规范**：`daede-YYYYMMDD-上午/下午HHMM.bin`

* **Git Tag 格式**：`vYYYY.MM.DD-HHMM`

---

## 🚀 刷机指南（U-Boot 网页恢复模式）

建议使用 U-Boot Web 恢复模式进行初次刷入或跨版本升级：

1. **配置电脑静态 IP**：
* 将网线连接至路由器的 **LAN 口**。
* 打开网卡设置，将电脑 IPv4 手动指定为：
* IP 地址：`192.168.1.2`
* 子网掩码：`255.255.255.0`
* 默认网关：`192.168.1.1`




2. **进入 U-Boot 恢复模式**：
* 路由器彻底断开电源。
* 按住机身上的 **Reset 重置键** 不要松手，然后插上电源线。
* 保持长按 Reset 键约 **5~8 秒**，直到指示灯呈现交替闪烁状态后松手。


3. **上传并刷入**：
* 在电脑浏览器中访问 `[http://192.168.1.1](http://192.168.1.1)` 进入 U-Boot 界面。
* 点击选择文件，选中下载的 `daede-*.bin` 固件。
* 点击 **Update** 开始刷写，等待路由器自动烧录并重启（过程约需 2~3 分钟）。


4. **重新登录**：
* 将电脑网卡改回 **自动获取 IP (DHCP)**。
* 浏览器访问 `192.168.1.1` 进入 OpenWrt 后台（默认无密码）。



---

## 🔍 刷机后验收测试命令

SSH 连接到路由器终端（`ssh root@192.168.1.1`），依次执行以下命令以验证底层各项优化是否完整就绪：

| 检查项目 | 验证命令

 | 预期输出

 | 说明

 |
| --- | --- | --- | --- |
| **Netkit 加速** | `ip link add dev test-nk type netkit`<br> | 无报错，直接返回终端提示符

 | Netkit 极速模式底层支持就绪

 |
| **BTF 核心字典** | `ls -lh /sys/kernel/btf/vmlinux`<br> | 呈现约 3.5MB ~ 5MB 大小的文件

 | eBPF CO-RE 字典已成功固化

 |
| **BBR 拥塞控制** | `sysctl net.ipv4.tcp_congestion_control`<br> | `= bbr`<br> | 出站吞吐提速算法已激活

 |
| **发包队列调度** | `sysctl net.core.default_qdisc`<br> | `= fq`<br> | 原生 FQ 队列已绑定生效

 |
| **BPF JIT 状态** | `cat /proc/sys/net/core/bpf_jit_enable`<br> | `1`<br> | BPF 即时编译执行器运行正常

 |
| **cgroup 挂载** | `mount | grep -E "bpf|cgroup2"`<br> | 包含 `type bpf` 与 `type cgroup2`<br> | 进程分流与控制套接字就绪

 |
| **双内核版本** | `dae -v && daed -v`<br> | 打印对应二进制编译版本号

 | 纯文本与图形化内核均正常就位

 |

*(测试完毕后可运行 `ip link delete dev test-nk` 清理临时测试接口)*

---

## 🛠️ 自动化流水线说明

本仓库基于 GitHub Actions 实现全自动闭环维护：

1. **上游变动巡检**：每天自动探测 [kenzok8/openwrt-daede](https://www.google.com/search?q=https://github.com/kenzok8/openwrt-daede) 仓库的最新 Commit SHA。
2. **免干预增量构建**：仅当检测到上游有更新代码时才触发构建，避免浪费云端额度。
3. **版本自动归档**：编译成功后自动向 Releases 生成新版本，附带规范的日期 Tag 与时间戳下载名。

---

## 📄 鸣谢与开源支持

* [OpenWrt Project](https://www.google.com/search?q=https://github.com/openwrt/openwrt)
* [KaguyaRing/openwrt-custom-gl-mt3600be](https://www.google.com/search?q=https://github.com/KaguyaRing/openwrt-custom-gl-mt3600be)
* [kenzok8/openwrt-daede](https://www.google.com/search?q=https://github.com/kenzok8/openwrt-daede)

* [eamonxg/luci-theme-aurora](https://www.google.com/search?q=https://github.com/eamonxg/luci-theme-aurora)
