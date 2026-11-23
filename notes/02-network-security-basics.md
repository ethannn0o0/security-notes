# 02 · Network Security Basics | 网络安全基础

> 🏷️ `network` `tcp-ip` `defense` `beginner`  
> 📅 Written: 2026-11-24 · Status: first draft / 初稿

> ⚠️ For legal, authorized learning only. 仅用于合法授权的学习。

---

## 1. Concept | 概念

Network security protects the **confidentiality, integrity and availability (CIA)** of data as it moves between systems. To defend a network you first need to understand how traffic flows: which protocols carry it, which ports expose it, and where it can be observed or altered.

网络安全的目标是在数据于系统之间传输时，保护其**机密性、完整性和可用性（CIA 三要素）**。要防御网络，首先要理解流量如何流动：由哪些协议承载、通过哪些端口暴露、在哪些位置可以被观察或篡改。

## 2. How it works | 原理

### 2.1 Layered models | 分层模型

| OSI Layer · OSI 层 | Example · 例子 | TCP/IP Layer · TCP/IP 层 |
| :-- | :-- | :-- |
| 7 Application · 应用层 | HTTP, DNS, SSH, SMTP | Application · 应用层 |
| 6 Presentation · 表示层 | TLS, encoding · 加密与编码 | Application · 应用层 |
| 5 Session · 会话层 | Session setup · 会话管理 | Application · 应用层 |
| 4 Transport · 传输层 | TCP, UDP | Transport · 传输层 |
| 3 Network · 网络层 | IP, ICMP | Internet · 网际层 |
| 2 Data Link · 数据链路层 | Ethernet, ARP, Wi-Fi | Network Access · 网络接入层 |
| 1 Physical · 物理层 | Cables, radio · 线缆、无线电 | Network Access · 网络接入层 |

Each layer has its own weaknesses, so defenses are needed at **several layers** (defense in depth).

每一层都有各自的弱点，所以需要在**多个层面**做防护（纵深防御）。

### 2.2 TCP three-way handshake | TCP 三次握手

```text
Client                          Server
  | ---- SYN ----------------->  |   "I want to connect"   我想建立连接
  | <--- SYN + ACK -----------   |   "OK, ready"           好的，已准备好
  | ---- ACK ----------------->  |   "Connection open"     连接建立
```

Many scanning and flooding techniques are built around abusing this exchange, which is why understanding it matters for both attackers and defenders.

许多扫描和泛洪手段都围绕这个握手过程展开，所以无论攻击方还是防御方都必须理解它。

### 2.3 Common protocols and ports | 常见协议与端口

| Port · 端口 | Service · 服务 | Security note · 安全提示 |
| :-: | :-- | :-- |
| 21 | FTP | Plaintext; prefer SFTP. 明文传输，优先用 SFTP。 |
| 22 | SSH | Encrypted remote access; use keys, disable weak logins. 加密远程登录，使用密钥并禁用弱口令。 |
| 23 | Telnet | Plaintext and obsolete; avoid. 明文且过时，应避免使用。 |
| 25 | SMTP | Mail transfer; watch for open relays. 邮件传输，注意开放中继。 |
| 53 | DNS | Often abused for spoofing and tunneling. 常被用于欺骗和隧道。 |
| 80 | HTTP | Plaintext web; redirect to HTTPS. 明文网页，应跳转到 HTTPS。 |
| 443 | HTTPS | Web over TLS. 基于 TLS 的网页。 |
| 445 | SMB | File sharing; keep off the internet. 文件共享，不应暴露到公网。 |
| 3389 | RDP | Remote desktop; frequently targeted, protect with VPN/MFA. 远程桌面，常被攻击，应配合 VPN 与多因素认证。 |

## 3. Attack view | 攻击视角（仅限靶场 / lab only）

Conceptually, attackers go after the network in a few recurring ways:

从概念上看，攻击者针对网络通常有这几类思路：

| Technique · 手法 | Idea · 思路 |
| :-- | :-- |
| Reconnaissance / port scanning · 侦察与端口扫描 | Discover which hosts and services are reachable. 找出哪些主机和服务可被访问。 |
| Sniffing · 嗅探 | Read traffic that is not encrypted. 读取未加密的流量。 |
| Man-in-the-Middle (MITM) · 中间人 | Sit between two parties to read or alter traffic (e.g. via ARP or DNS spoofing). 位于通信双方之间读取或篡改流量（如借助 ARP、DNS 欺骗）。 |
| Denial of Service (DoS) · 拒绝服务 | Exhaust bandwidth or resources so legitimate users are blocked. 耗尽带宽或资源，让正常用户无法访问。 |
| Lateral movement · 横向移动 | After one foothold, move to other hosts in a flat network. 取得一个立足点后，在扁平网络中向其他主机扩散。 |

I do not list tools or step-by-step attack procedures against real targets here. Observe and learn only inside your own lab (section 5).

这里不列出针对真实目标的工具或分步骤攻击流程。请只在你自己的实验环境里观察和学习（见第 5 节）。

## 4. Defense view | 防御视角

| Defense · 防护 | What it does · 作用 |
| :-- | :-- |
| **Firewall** · 防火墙 | Allows or blocks traffic by rules (default deny, allow only what is needed). 按规则放行或阻断流量（默认拒绝，只放行必要流量）。 |
| **Network segmentation** · 网络分段 | Split the network (e.g. user, server, guest zones/VLANs) to limit lateral movement. 将网络划分为不同区域（如办公、服务器、访客），限制横向移动。 |
| **Encryption** · 加密 | Use TLS, SSH, VPN so intercepted traffic is unreadable. 使用 TLS、SSH、VPN，让被截获的流量无法读取。 |
| **IDS / IPS** · 入侵检测 / 防御 | Detect (and optionally block) suspicious patterns. 检测（并可选择阻断）可疑流量模式。 |
| **Least exposure** · 最小暴露面 | Close unused ports and services; keep management interfaces private. 关闭无用端口和服务，管理接口不对外开放。 |
| **Patching and hardening** · 补丁与加固 | Keep devices and firmware updated, change default credentials. 及时更新设备与固件，修改默认口令。 |
| **Logging and monitoring** · 日志与监控 | Collect flow and firewall logs and alert on anomalies. 收集流量与防火墙日志，对异常告警。 |

## 5. Lab / Practice | 实操（合法环境）

Only analyze networks and hosts you **own** or have **explicit permission** to test, such as your own machine, home network or a virtual lab (VirtualBox / VMware / Docker).

只分析你**自己拥有**或**已获明确授权**的网络和主机，例如自己的电脑、家庭网络或虚拟实验环境（VirtualBox / VMware / Docker）。

**Safe starter exercises | 安全的入门练习**

```bash
# See which ports YOUR OWN machine is listening on (Linux)
ss -tuln

# Check reachability and path to a host you are allowed to test
ping <your-own-host>
traceroute <your-own-host>

# Scan only your own machine / lab VM
nmap -sV 127.0.0.1
```

In **Wireshark**, capture your own traffic and try display filters such as `tcp.flags.syn == 1`, `dns`, and `http` to watch a handshake, a DNS lookup and a plaintext request. Notice what HTTPS hides.

在 **Wireshark** 里捕获你自己的流量，尝试 `tcp.flags.syn == 1`、`dns`、`http` 等显示过滤器，观察握手、DNS 查询和明文请求，并留意 HTTPS 隐藏了什么。

**My progress / 我的进度**
- [ ] Draw my home network and list open ports / 画出家庭网络并列出开放端口
- [ ] Capture and explain one TCP handshake in Wireshark / 在 Wireshark 里抓取并解释一次 TCP 握手
- [ ] Compare HTTP vs HTTPS traffic in a capture / 对比抓包中 HTTP 与 HTTPS 的差异
- [ ] Build a small two-VM lab with a firewall rule / 搭建两台虚拟机并配置一条防火墙规则

## 6. References | 参考

- Wireshark documentation: <https://www.wireshark.org/docs/>
- Nmap reference guide: <https://nmap.org/book/man.html>
- Cloudflare Learning Center (networking): <https://www.cloudflare.com/learning/>
- MITRE ATT&CK: <https://attack.mitre.org/>

---

⬅️ [Back to index | 返回目录](../README.md)
