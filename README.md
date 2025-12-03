# Linux 一键安装 Clash

## 快速开始

### 一键安装

- 首先需要保证本机已配置好Clash代理, 并且开启7890端口的局域网共享
- 然后在ssh连接使用Remote-SSH方式, 转发到远程的7892端口, 配置示例如下:
```
Host ethancao
    HostName xxx
    User ethan
    RemoteForward 7892 localhost:7890
```

- 执行以下命令进行安装：
```bash
git clone --branch master --depth 1 https://gh-proxy.com/https://github.com/EthanCaol/clash-for-linux-install.git && cd clash-for-linux-install && sudo bash install.sh
```

- 安装完成后, 启用系统代理:
```bash
clashctl proxy
```

- 在.bashrc中添加以下代码, 每次登录自动启用代理:
```bash
proxy_addr=127.0.0.1
proxy_port=7890
function proxy_on() {
    export http_proxy=http://$proxy_addr:$proxy_port
    export https_proxy=http://$proxy_addr:$proxy_port
    export no_proxy=$proxy_addr,localhost
    export HTTP_PROXY=http://$proxy_addr:$proxy_port
    export HTTPS_PROXY=http://$proxy_addr:$proxy_port
    export NO_PROXY=$proxy_addr,localhost
}
function proxy_off(){
    unset http_proxy
    unset https_proxy
    unset no_proxy
    unset HTTP_PROXY
    unset HTTPS_PROXY
    unset NO_PROXY
}
proxy_on
```

### Web 控制台

- 需要在防火墙放行9090端口
```bash
$ clashui
╔═══════════════════════════════════════════════╗
║                😼 Web 控制台                  ║
║═══════════════════════════════════════════════║
║                                               ║
║     🔓 注意放行端口：9090                      ║
║     🏠 内网：http://192.168.0.1:9090/ui       ║
║     🌏 公网：http://255.255.255.255:9090/ui   ║
║     ☁️ 公共：http://board.zash.run.place      ║
║                                               ║
╚═══════════════════════════════════════════════╝

$ clashsecret 666
😼 密钥更新成功，已重启生效

$ clashsecret
😼 当前密钥：666
```

```bash
Usage: 
  clashctl COMMAND [OPTIONS]

Commands:
    on                    开启代理
    off                   关闭代理
    proxy                 系统代理
    ui                    面板地址
    status                内核状况
    tun                   Tun 模式
    mixin                 Mixin 配置
    secret                Web 密钥
    update                更新订阅
    upgrade               升级内核

Global Options:
    -h, --help            显示帮助信息
``` 

💡`clashon` 同 `clashctl on`，`Tab` 补全更方便！

### 优雅启停

```bash
$ clashon
😼 已开启代理环境

$ clashoff
😼 已关闭代理环境
```
- 在启停代理内核的同时，自动同步设置系统代理。
- 亦可通过 `clashproxy` 单独控制系统代理。



- 通过浏览器打开 Web 控制台，实现可视化操作：切换节点、查看日志等。
- 若暴露到公网使用建议定期更换密钥。

### 升级内核
```bash
$ clashupgrade
😼 请求内核升级...
{"status":"ok"}
😼 内核升级成功
```
- 代理内核会自动处理升级流程，并从 `GitHub` 获取最新软件包。为避免因网络原因导致拉取失败，建议为相关域名配置代理规则。
- 可使用 `-v` 参数查看代理内核的升级日志。


### 更新订阅

```bash
$ clashupdate https://example.com
👌 正在下载：原配置已备份...
🍃 下载成功：内核验证配置...
🍃 订阅更新成功

$ clashupdate auto [url]
😼 已设置定时更新订阅

$ clashupdate log
✅ [2025-02-23 22:45:23] 订阅更新成功：https://example.com
```

- `clashupdate` 会记住上次更新成功的订阅链接，后续执行无需再指定。
- 可通过 `crontab -e` 修改定时更新频率及订阅链接。
- 通过配置文件进行更新：[pr#24](https://github.com/nelvko/clash-for-linux-install/pull/24#issuecomment-2565054701)

### `Tun` 模式

```bash
$ clashtun
😾 Tun 状态：关闭

$ clashtun on
😼 Tun 模式已开启
```

- 作用：实现本机及 `Docker` 等容器的所有流量路由到 `clash` 代理、DNS 劫持等。
- 原理：[clash-verge-rev](https://www.clashverge.dev/guide/term.html#tun)、 [clash.wiki](https://clash.wiki/premium/tun-device.html)。
- 注意事项：[#100](https://github.com/nelvko/clash-for-linux-install/issues/100#issuecomment-2782680205)

### `Mixin` 配置

```bash
$ clashmixin
😼 查看 Mixin 配置

$ clashmixin -e
😼 编辑 Mixin 配置

$ clashmixin -o
😼 查看原始订阅配置

$ clashmixin -r
😼 查看运行时配置
```
- 通过 `Mixin` 自定义的配置内容会与原始订阅深度合并生成运行时配置，其中 `Mixin` 的优先级最高。
- `Mixin` 可通过前置、后置或覆盖方式，对原始订阅中的规则、节点和策略组进行新增或修改。
- 内核启动时加载的是运行时配置，因此直接修改原始订阅内容并不会生效。

### 卸载

```bash
sudo bash uninstall.sh
```

