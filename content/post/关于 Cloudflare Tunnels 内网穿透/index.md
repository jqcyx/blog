---
title: "关于 Cloudflare Tunnels 内网穿透"
date: 2026-03-21T19:27:00+08:00
draft: false
---

## 准备

你需要的：

一个 Cloudflare 账号。

一个域名。

如果以上东西没有，可以去[这里](https://blog.jqcyx.qzz.io/p/%E5%A6%82%E4%BD%95%E6%90%AD%E5%BB%BA%E8%87%AA%E5%B7%B1%E7%9A%84%E5%8D%9A%E5%AE%A2/)参考。

一台电脑。

脑子。

手。

如果以上东西没有，那么请自行离开。


## 操作

登录到 Cloudflare。

![](Cloudflare界面.jpg)

选择侧边栏的 联网 > Tunnels。

这个好是很久以前注册的，当时需要银行卡认证，~~不过可以绕过就是了（感谢 Cloudflare），~~，但是看界面应该改过版了。之前是 Zero Trust 的一部分，现在被独立出来，应该不用绑卡~~，感谢 Cloudflare~~。

点击创建隧道，输入隧道名称。

下载你自己版本的 cloudflared。相信你都能看到这个网站了，上 Github 问题应该不大。

运行给出的指令，在 Windows 下应该会输出这样的东西：

```
2026-03-21T11:44:29Z INF Installing cloudflared Windows service
2026-03-21T11:44:29Z INF cloudflared agent service is installed windowsServiceName=Cloudflared
2026-03-21T11:44:29Z INF Agent service for cloudflared installed successfully windowsServiceName=Cloudflared
```

这就算完事了，你应该能看到浏览器上 `隧道连接成功` 的字样。回到 Tunnels 主页会显示状态为 `健康`。

点击其名称，选择 `Routes`，开始创建隧道。

点击 `Add routes`，选择 `Published application`，选择域名（一般要填）及后缀（一般不填），看到下面的 `Service URL`，你需要在里面填上本机访问地址。

一些例子：

`Openlist` 默认开在 `5244` 端口，协议为 `http`，填入 `http://localhost:5244`；

`ssh` 开在 `22` 端口，输入 `ssh://localhost:22`。

发布后，就可以从公网访问了。

更多玩法可以在 [这里](https://developers.cloudflare.com/tunnel/routing/) 探索。

完结撒花！

不对啊，我的 ssh 怎么连不上？？？

## ssh

你需要在客户机上也安装 `cloudflared`。

新开窗口，运行：

```
cloudflared access ssh --hostname 你的映射域名 -url localhost:222
```

`222` 可以改为其他端口。

然后把这个窗口撂一边，**不要关！**，新开窗口运行

```
ssh -p 222 你的用户名@localhost
```

就好了。

## Cloudflare Quick Tunnels

这是个啥东东？

你可以打开 [Cloudflare Quick Tunnels](https://tyrcloudflare.com) 查看。简而言之就是你可以用

```
cloudflared tunnel --url 你的本机连接
```

创建一个公用域名的隧道，适合临时用。

好了，我要去打 AtCoder 了。