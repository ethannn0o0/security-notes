# 10 · Digital Forensics & Incident Investigation | 数字取证与事件调查

> 🏷️ `forensics` `dfir` `evidence` `timeline` `beginner`  
> 📅 Written: 2026-11-24 · Status: first draft / 初稿

> ⚠️ For legal, authorized learning only. Only examine devices and data you own or are explicitly authorized to examine. 仅用于合法授权的学习。只检查你自己拥有或已获明确授权的设备和数据。

---

## 1. Concept | 概念

**Digital forensics** is the careful collection, preservation and analysis of digital evidence to answer questions such as *what happened, when, how, and who or what was involved*. **DFIR** combines forensics with **incident response**. The key difference from ordinary troubleshooting is that evidence must stay **trustworthy**: it should be possible to show that it was not altered.

**数字取证**是对数字证据进行谨慎的收集、保全和分析，用来回答*发生了什么、何时发生、如何发生、涉及谁或什么*。**DFIR** 把取证与**事件响应**结合起来。与普通排障最大的不同是证据必须**可信**：要能证明它没有被改动。

| Principle · 原则 | Meaning · 含义 |
| :-- | :-- |
| **Preserve** · 保全 | Do not change the original evidence. 不改动原始证据。 |
| **Verify** · 校验 | Hash evidence before and after to prove integrity. 在前后各做一次哈希，证明完整性。 |
| **Document** · 记录 | Record every action, time and person (chain of custody). 记录每个操作、时间和经手人（证据链）。 |
| **Work on copies** · 用副本 | Analyze a forensic copy, not the original. 分析取证副本，而不是原件。 |
| **Stay authorized** · 保持授权 | Examine only what you are permitted to examine. 只检查被允许检查的内容。 |

## 2. How it works | 原理

### 2.1 Order of volatility | 易失性顺序

Collect the most **short-lived** data first, because it disappears first (see RFC 3227):

先收集**最容易消失**的数据（参见 RFC 3227）：

```text
Most volatile 最易失                                              Least volatile 最稳定
CPU registers/cache → RAM (memory) → network state → running processes → disk → remote logs → backups/archives
寄存器与缓存          内存            网络状态        运行中的进程         磁盘     远程日志     备份与归档
```

### 2.2 The investigation workflow | 调查流程

```text
1. Identify  →  2. Preserve  →  3. Collect  →  4. Examine/Analyze  →  5. Report
   识别          保全           收集           检查与分析               报告
```

### 2.3 Common evidence sources | 常见证据来源

| Source · 来源 | What it can show · 能说明什么 |
| :-- | :-- |
| Memory image · 内存镜像 | Running processes, network connections, loaded code. 运行中的进程、网络连接、已加载代码。 |
| Disk image · 磁盘镜像 | Files, deleted files, timestamps, user activity. 文件、已删除文件、时间戳、用户活动。 |
| System logs · 系统日志 | Logins, services, errors (Windows Event Logs, Linux `/var/log`). 登录、服务、错误。 |
| Browser artifacts · 浏览器痕迹 | History, downloads, cookies. 历史、下载、Cookie。 |
| Network captures · 流量捕获 | Who talked to whom and what was transferred. 谁与谁通信、传输了什么。 |
| Cloud and app logs · 云与应用日志 | API activity, authentication events. API 活动、认证事件。 |

### 2.4 Timeline analysis | 时间线分析

Merging timestamps from many sources into **one ordered timeline** is often what turns scattered clues into a story. Always check **time zones and clock drift** before trusting a timeline.

把多个来源的时间戳合并成**一条有序时间线**，往往能把零散线索变成完整的故事。信任时间线之前，务必先核对**时区和时钟偏差**。

## 3. Attack view | 攻击视角（仅限靶场 / lab only）

Investigators benefit from knowing how an intruder might try to hide. These are **anti-forensics** ideas, described only so you can spot them:

了解入侵者可能如何掩盖痕迹，对调查者有帮助。以下是**反取证**思路，仅为方便你识别：

| Technique · 手法 | What an investigator may notice · 调查者可能发现 |
| :-- | :-- |
| Clearing or editing logs · 清除或篡改日志 | Gaps in logs, log-cleared events, missing time ranges. 日志空缺、日志清除事件、缺失的时间段。 |
| Altering timestamps · 篡改时间戳 | Files whose times do not match other evidence. 时间与其他证据不符的文件。 |
| Wiping or deleting files · 擦除或删除文件 | Traces in file-system metadata, backups or other copies. 文件系统元数据、备份或其他副本中的痕迹。 |
| Using trusted tools · 利用系统自带工具 | Unusual command lines in process and shell history. 进程和 shell 历史中的异常命令行。 |

This is why logs should be **forwarded off the host** (see note 05). 所以日志应该**转发出主机**（见笔记 05）。

## 4. Defense view | 防御视角

| Area · 方面 | Good practice · 良好实践 |
| :-- | :-- |
| **Be ready before an incident** · 事前准备 | Know what logs you have, how long you keep them, and who may collect evidence. 清楚有哪些日志、保存多久、谁有权收集证据。 |
| **Synchronize clocks** · 时钟同步 | Use NTP so timestamps line up across systems. 使用 NTP，让各系统时间戳对得上。 |
| **Protect logs** · 保护日志 | Central, append-only or write-once storage. 集中、仅追加或一次写入的存储。 |
| **Evidence handling** · 证据处理 | Written procedure, chain-of-custody forms, secure storage. 书面流程、证据链表单、安全存放。 |
| **Tooling** · 工具 | Keep a tested forensic toolkit and trained people. 准备经过测试的取证工具和受过训练的人员。 |
| **Legal and privacy** · 法律与隐私 | Involve legal and HR early; handle personal data carefully. 尽早让法务和人事参与；谨慎处理个人数据。 |

## 5. Lab / Practice | 实操（合法环境）

Practise only on **your own test media** or **public training data**. A basic imaging exercise on a spare USB stick that contains nothing important:

只在**你自己的测试介质**或**公开的训练数据**上练习。下面是在一个没有重要数据的闲置 U 盘上做基础镜像的练习：

```bash
# ⚠️ Double-check the device name. Writing to the wrong device destroys data.
# ⚠️ 务必核对设备名，写错设备会毁掉数据。
lsblk                                   # identify the test USB device, e.g. /dev/sdX

# Read-only copy of YOUR test device into an image file
sudo dd if=/dev/sdX of=test_usb.img bs=4M status=progress

# Prove the copy matches: hash the device and the image, then compare
sudo sha256sum /dev/sdX test_usb.img
```

Then open the image in **Autopsy** and build a simple timeline. Public data sets | 公开数据集：**NIST CFReDS** and **Digital Corpora** provide forensic images made for training. For memory analysis, practise with public sample memory images and **Volatility**. Guided labs | 有引导的靶场：**CyberDefenders**, **TryHackMe** DFIR rooms.

**My progress / 我的进度**
- [ ] Explain order of volatility and chain of custody / 讲清易失性顺序和证据链
- [ ] Image a test USB and verify matching hashes / 为测试 U 盘制作镜像并核对哈希一致
- [ ] Build a timeline of a public training image in Autopsy / 在 Autopsy 中为公开训练镜像建立时间线
- [ ] Write a short investigation report / 写一份简短的调查报告

## 6. References | 参考

- RFC 3227, Guidelines for Evidence Collection and Archiving: <https://www.rfc-editor.org/rfc/rfc3227>
- NIST SP 800-86, Guide to Integrating Forensic Techniques into Incident Response: <https://csrc.nist.gov/pubs/sp/800/86/final>
- Autopsy: <https://www.autopsy.com/>
- Volatility Foundation: <https://volatilityfoundation.org/>
- NIST CFReDS: <https://cfreds.nist.gov/>
- Digital Corpora: <https://digitalcorpora.org/>

---

⬅️ [Back to index | 返回目录](../README.md)
