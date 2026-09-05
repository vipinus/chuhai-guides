# 06 · 群晖、威联通 NAS 怎么走线路

NAS 是家里最需要走线路又最没人管的设备：下载器要连海外资源、Docker 要拉镜像、Cloud Sync 要同步海外网盘、Plex 要刮削元数据。NAS 装不了 Hiddify，但群晖和威联通都自带 OpenVPN 客户端——在本站专网页面下载一个 .ovpn，导入即连，开机自动重连。这篇讲群晖和威联通各自的导入位置、「使用默认网关」这个关键选项、改密码后的处理，以及为什么「从外面访问 NAS」是另一件事、要用私网。

## 先分清两件事

让 NAS 自己走线路（下载、拉镜像、同步海外网盘）：用专网 OpenVPN，本篇讲的。

从外面访问 NAS（出差时开共享、看监控）：用私网 Tailscale，把 NAS 和你的手机放进同一个私有网络，见《在国外看国内家里的监控和 NAS》。专网做不到这件事——它把 NAS 接到我们的线路，不会把你接进家里。

## 群晖 Synology

1. 登录本站，在专网页面点地区国旗，下载该地区的 .ovpn 文件。
2. 控制面板 → 网络 → 网络界面 → 新增 → 创建 VPN 配置文件 → OpenVPN（通过导入 .ovpn 文件），填本站账号邮箱和密码。
3. 「使用默认网关」：勾上，NAS 的所有出站流量走线路（下载、Docker、Cloud Sync 都受益）；不勾，只是建立隧道、出站仍走本地。绝大多数场景要勾。
4. 「服务器上的连接丢失时重新连接」勾上，NAS 重启或线路波动后自动恢复。连接，然后到套件里试一次拉取。

## 威联通 QNAP

1. 同样下载 .ovpn。
2. QVPN Service → VPN 客户端 → 添加 → OpenVPN，导入文件，填账号密码。
3. 在连接设置里勾选「使用 VPN 作为 NAS 的默认网关」和自动重连，连接。

## 哪些流量会走、哪些不会

- 走：Download Station / Download Station 的 BT 与 HTTP 任务、Docker 拉镜像、Cloud Sync 同步 Google Drive / Dropbox / OneDrive、Plex 和 Emby 刮削元数据、套件中心更新。
- 不走：局域网内你访问 NAS 的流量——它本来就在局域网里，不受影响。
- 人在国内想让国内网站直连：NAS 上没有分流脚本，专网连上后 NAS 的全部出站走线路；国内下载源慢的话，在下载任务里用国内镜像地址，或者只在需要时连线路。

## 要知道的

- 配置文件里带着你的账号，NAS 上的密码字段改密码后会失效：重新下载 .ovpn 替换，或者只改 VPN 配置里的密码。
- 账号到期 NAS 会断线，续费后自动重连，配置不用换。
- NAS 算一台设备，与其他设备共用 5 台同时在线额度。
- 线路走 UDP；NAS 放在家里宽带下几乎不会遇到限 UDP 的问题。

## 常见问题

**勾了「使用默认网关」后局域网还能访问 NAS 吗？** 能。局域网流量不经过网关，共享、Plex 播放、管理页都不受影响。

**NAS 走线路后 Plex 远程播放变慢？** Plex 的远程播放走 NAS 的出站，勾了默认网关后会经线路绕一圈。要远程播放的话，用私网访问 NAS 更直接；或者只在需要下载时连专网。

**能只让 Download Station 走线路吗？** 群晖和威联通没有按应用分流。要精细控制，在 Docker 里跑一个带 OpenVPN 的下载容器（如 qbittorrent + gluetun）只让它走线路。

**Linux 系统的 NAS（TrueNAS、Unraid）怎么办？** 按 Linux 的方式：openvpn 包 + 配置文件放到 /etc/openvpn/client/，见《Linux 服务器和命令行工具怎么走线路》。

## 延伸阅读

- [专网页：客户端下载与配置文件](https://7d24hrs.com/zh-CN/openvpn)
- [OpenVPN 怎么用、什么时候选它](https://7d24hrs.com/zh-CN/guides/openvpn-setup)
- [在国外看国内家里的监控和 NAS](https://7d24hrs.com/zh-CN/guides/home-camera)
- [Linux 服务器和命令行工具怎么走线路](https://7d24hrs.com/zh-CN/guides/linux-server)

本文网站版（含繁体与英文）：https://7d24hrs.com/zh-CN/guides/nas-openvpn

---
由 [蓝盾](https://7d24hrs.com) 团队整理 · 问题来 [Telegram 群](https://t.me/+NWJN_9yITj9kOWFh) · 注册领 24 小时免费试用，邀请朋友每位送 30 天，长期有效
