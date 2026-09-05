# 05 · Linux 服务器和命令行工具怎么走线路

Linux 上走线路有两条路，按需求选：整台机器都走（拉 Docker 镜像、跑需要海外网络的服务、无人值守）用专网 OpenVPN，一个配置文件放进 /etc/openvpn/client/ 开机自启；只让几个命令走（git、pip、npm、curl）用网页代理，把代理地址填进 http_proxy 环境变量，其余流量不动。桌面 Linux 还可以在网络管理器里导入。这篇三种都讲，附密码文件的写法和验证命令。

## 路一：整机走线路（专网 OpenVPN）

1. 装客户端：Debian / Ubuntu sudo apt install openvpn，Fedora sudo dnf install openvpn，Arch sudo pacman -S openvpn。
2. 登录本站，在专网页面点地区国旗下载 .ovpn。Linux 拿到的配置不内置账号密码（网络管理器不接受内置账密的文件），所以另建一个密码文件：两行，第一行本站邮箱，第二行密码，chmod 600。
3. 把 .ovpn 复制为 /etc/openvpn/client/<地区>.conf，在文件里的 auth-user-pass 后面加上密码文件的路径。
4. sudo systemctl enable --now openvpn-client@<地区>。看状态 systemctl status openvpn-client@<地区>，验证 curl -s https://ipinfo.io/country 显示的是你选的地区。
5. 换地区就再下一份配置、再启用一个单元；同时只跑一个。

## 路二：只让命令行工具走（网页代理）

本站网页代理页面给的代理地址和你的账号密码，可以直接填进环境变量：export https_proxy=https://用户名:密码@代理地址 export http_proxy=$https_proxy。密码里有特殊字符要 URL 编码。之后 curl、wget、git、pip、npm 在这个终端里都走线路，其他程序不受影响。

Docker 守护进程不读 shell 环境变量，要写进 /etc/systemd/system/docker.service.d/proxy.conf 的 Environment= 再重启 docker；apt 写 /etc/apt/apt.conf.d/proxy.conf 的 Acquire::https::Proxy。

这条路不掉线、不需要 root、不改路由，适合公司服务器和只想加速拉取的场景；代价是只覆盖认代理变量的程序。

## 路三：桌面 Linux 用网络管理器

1. 装 network-manager-openvpn-gnome（本站专网页面点一下可唤起软件中心）。
2. 设置 → 网络 → VPN → 「+」→ 从文件导入，选 .ovpn，填本站邮箱和密码，保存。
3. 顶栏或托盘里开关 VPN。要开机自动连，在有线/无线连接的设置里勾「自动连接到 VPN」。

## 验证与排查

- 看出口：curl -s https://ipinfo.io 的 country 是你选的地区。
- 看 DNS：resolvectl status 里 VPN 接口的 DNS 应是线路下发的；泄露的话在配置里加 dhcp-option DNS 或用 update-systemd-resolved 脚本。
- AUTH_FAILED：密码文件写错或账号到期；改过密码要更新密码文件。
- TLS handshake timeout：到服务器的 UDP 不通，换地区或换网络；云服务器的安全组要放行出站 UDP。
- 服务器在国内、要国内源直连：专网连上后全部出站走线路，把 apt / pip / npm 的源改成国内镜像，或改用路二只让特定命令走。

## 要知道的

- 一台 Linux 算一台设备，与其他设备共用 5 台同时在线额度；路二的网页代理按连接计，也算在内。
- 配置文件和密码文件等于你的账号，别提交进 git 仓库、别放进镜像。
- 改密码后旧配置立刻失效，这是设计上的吊销手段；更新密码文件即可。
- 云服务器上跑线路请遵守服务商的使用条款；线路只记录连接时长和流量总量。

## 常见问题

**为什么 Linux 的配置不内置账号密码？** 网络管理器导入配置时不接受内置账密的文件，会报导入失败，所以发给 Linux 的那份让你自己填；用 systemd 方式就写进密码文件。

**能只让 Docker 走线路吗？** 能，给 Docker 守护进程单独配代理变量（路二）；或者跑一个带 OpenVPN 的容器（gluetun）让特定容器走线路。

**私网 Tailscale 在 Linux 上呢？** 官方脚本一行安装，tailscale up --login-server=<本站控制服务器> 登录，适合要从外面访问这台机器、或者要一次配置永远在线的场景；只是拉取加速用专网或代理更简单。

**路由器上的 OpenWrt 也这样吗？** OpenWrt 装 luci-app-openvpn 上传 .ovpn 即可，整个局域网走线路；装了本站固件的路由器不需要，登录账号就行。

## 延伸阅读

- [专网页：客户端下载与配置文件](https://7d24hrs.com/zh-CN/openvpn)
- [OpenVPN 怎么用、什么时候选它](https://7d24hrs.com/zh-CN/guides/openvpn-setup)
- [群晖、威联通 NAS 怎么走线路](https://7d24hrs.com/zh-CN/guides/nas-openvpn)
- [网页代理是什么、什么时候用](https://7d24hrs.com/zh-CN/guides/web-proxy)

本文网站版（含繁体与英文）：https://7d24hrs.com/zh-CN/guides/linux-server

---
由 [蓝盾](https://7d24hrs.com) 团队整理 · 问题来 [Telegram 群](https://t.me/+NWJN_9yITj9kOWFh) · 注册领 24 小时免费试用，邀请朋友每位送 30 天，长期有效
