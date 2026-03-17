# luci-app-adguardhome

适用于 OpenWrt 24.10 路由器的 AdGuard Home LuCI 管理界面。
做了部分修复和改进，感谢前辈们

## 功能特性

### 核心管理
- 网页管理端口配置
- LuCI 在线下载/更新 AdGuard Home 核心（支持自定义下载链接）
- UPX 压缩核心（节省 ROM 空间）
- 自定义二进制文件路径（支持 tmp 分区，重启后自动下载）
- 自定义配置文件路径
- 自定义工作目录
- 自定义运行日志路径

### DNS 重定向模式
| 模式 | 说明 |
|------|------|
| dnsmasq 上游 | 作为 dnsmasq 的上游服务器（AGH 中统计的 IP 均为 127.0.0.1） |
| 重定向 53 端口 | 将 53 端口重定向到 AdGuardHome（IPv6 需要安装 ip6tables nat redirect） |
| 端口替换 | 使用 53 端口替换 dnsmasq（需要设置 AGH DNS IP 为 0.0.0.0） |

### 配置管理
- 图形化配置文件编辑（支持 YAML 编辑器）
- 配置模板快速初始化
- 系统升级时保留配置（可选保留数据库和查询日志）

### 计划任务
- 自动更新核心（默认 3:30/天）
- 自动截短查询日志（每小时，限制 2000 行）
- 自动截短运行日志（默认 3:30/天，限制 2000 行）
- 自动更新 IPv6 主机并重启（每小时，无更新不重启）
- 自动更新 GFW 列表并重启（默认 3:30/天，无更新不重启）

### 其他功能
- GFW 列表管理（删除/添加/指定上游 DNS）
- 网页登录密码修改
- 实时运行日志查看（正序/倒序、每 3 秒刷新、本地时区转换）
- 手动配置文件修改
- 开机启动后等待网络就绪（3 分钟超时）
- 关机时自动备份工作目录

## 安装方法

### 方法一：OPKG 安装
```bash
opkg install luci-app-adguardhome_*.ipk
```

### 方法二：编译集成
将项目克隆到 OpenWrt SDK 的 package 目录：
```bash
cd your-openwrt-sdk/package
git clone https://github.com/AoThen/luci-app-adguardhome.git
make menuconfig  # 勾选 LuCI -> Applications -> luci-app-adguardhome
make package/luci-app-adguardhome/compile
```

## DNS 重定向模式选择

### 方案一：GFW 代理
DNS 重定向 → 作为 dnsmasq 上游服务器

### 方案二：GFW 代理
手动设置 ADH 上游 DNS 为 `127.0.0.1:[SSR 监听端口]`，然后使用「端口替换」模式

### 方案三：国外 IP 代理
任意重定向模式 + ADH 开启 GFW 列表 + 计划任务定时更新

### 方案四：GFW 代理
DNS 重定向 → 重定向 53 端口，设置 ADH 上游 DNS 为 `127.0.0.1:53`

## 关于 UPX 压缩

| 指标 | 未压缩 | UPX 压缩 | 差值 |
|------|--------|----------|------|
| 文件大小 | 14112 KB | 5309 KB | -8803 KB |
| 实际占用 | 6260 KB | 5324 KB | -936 KB |
| 运存占用 (VmRSS) | 14380 KB | 18496 KB | +4116 KB |

- 压缩文件系统（JFFS2）：收益有限
- 非压缩文件系统：性价比高，用运存换 ROM 空间

## 已知问题

- **数据库文件系统**：不支持 mmap 的文件系统（如 jffs2、data-stk-oo），需修改工作目录。检测到 jffs2 时会自动软链接到 /tmp，重启后 DNS 数据库会丢失
- **DDNS 插件异常**：如发现大量来自 127.0.0.1 的 localhost 查询，可能是 DDNS 插件导致。解决方法：删除或注释 `/etc/hotplug.d/iface/95-ddns`

## 相关项目

- [AdGuardHome](https://github.com/AdguardTeam/AdGuardHome) - AdGuard Home 核心

## 许可证

[Apache License 2.0](LICENSE) © AoThen
