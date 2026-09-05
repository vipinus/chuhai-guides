# 01 · 哪些服务需要海外 IP，线路是怎么工作的

判断标准和回国访问是镜像的：服务方看到你的来源 IP 在中国大陆，或者根本连不到它的服务器，就打不开、降级或极慢。下表按用途分。

| 类别 | 例子 | 没有线路会怎样 | 除了 IP 还要什么 |
|---|---|---|---|
| 办公 | Google Workspace、Slack、Zoom、Teams、Notion | 打不开或断断续续 | 公司账号 |
| 开发 | GitHub、npm、Docker Hub、PyPI 镜像外的源、Stack Overflow | 慢到超时 | 无 |
| AI | ChatGPT、Claude、Gemini | 打不开 | 账号注册地、手机号 |
| 学术 | Google Scholar、论文数据库、学校邮箱 | 打不开或极慢 | 学校账号 |
| 游戏 | Steam 海外区、PSN、Switch eShop、海外服 | 商店打不开、延迟高 | 区服账号 |
| 影音 | YouTube、Netflix、Disney+、Spotify | 打不开 | 付费账号，且账号地区要和 IP 匹配 |
| 社交 | X、Instagram、Telegram、Discord、WhatsApp | 打不开 | 手机号 |

## 一个重要的区分

**「需要海外 IP」和「需要海外账号」是两件事。** GitHub、YouTube 换个 IP 就好；Netflix 要付费账号且地区匹配；ChatGPT 注册要能收验证码的海外手机号。线路解决的是「能不能打开」，账号要靠你自己。

## 线路是怎么工作的

把你的流量先加密送到一台海外服务器，再由它访问目标。目标看到的是那台服务器的 IP。做法有客户端、网页代理、路由器三种，选择方法见 [选择哪种连接方式](https://github.com/vipinus/network-guides/blob/main/02-choose-your-connection-method.md)。

## 三个常见误区

- **改 DNS 没用。** 连不上是路径问题，不是解析问题。
- **速度看两点之间的线路。** 从国内出去，瓶颈是跨境链路和你的运营商出口，选地区比选服务器配置重要，见 [03](https://github.com/vipinus/chuhai-guides/blob/main/03-which-region-is-fastest.md)。
- **用着国内 App 时不用全走线路。** 开分流：国内网站直连、海外网站走线路，两边都不绕远，见 [路由器分流](https://github.com/vipinus/router-guides/blob/main/03-router-split-routing.md)。

---
由 [蓝盾](https://7d24hrs.com) 团队整理 · 问题来 [Telegram 群](https://t.me/+NWJN_9yITj9kOWFh) · 注册领 24 小时免费试用，邀请朋友每位送 30 天，长期有效
