# 03 · Linux & Scripting | Linux 与脚本

> 🏷️ `linux` `bash` `python` `hardening` `beginner`  
> 📅 Written: 2026-11-24 · Status: first draft / 初稿

> ⚠️ For legal, authorized learning only. 仅用于合法授权的学习。

---

## 1. Concept | 概念

Most servers, cloud workloads and security tools run on **Linux**, and most security automation is written in **shell** or **Python**. Knowing how Linux handles files, users, permissions and processes is the base for both defending a system and understanding how it gets abused.

大多数服务器、云工作负载和安全工具都运行在 **Linux** 上，而大多数安全自动化用 **Shell** 或 **Python** 编写。理解 Linux 如何管理文件、用户、权限和进程，是防御系统、也是理解系统如何被滥用的基础。

## 2. How it works | 原理

### 2.1 Everything is a file, and files have owners | 一切皆文件，文件有属主

```text
$ ls -l report.txt
-rw-r----- 1 alice staff 1024 Nov 24 10:00 report.txt
```

| Part · 部分 | Value · 值 | Meaning · 含义 |
| :-- | :-: | :-- |
| Type · 类型 | `-` | `-` file 文件，`d` directory 目录 |
| Owner perms · 所有者权限 | `rw-` | read + write · 读写 |
| Group perms · 组权限 | `r--` | read only · 只读 |
| Others perms · 其他人权限 | `---` | no access · 无权限 |
| Owner · 所有者 | `alice` | the user who owns the file · 文件所属用户 |
| Group · 所属组 | `staff` | the group that owns the file · 文件所属组 |


| Symbol · 符号 | Meaning · 含义 | Octal · 数字 |
| :-: | :-- | :-: |
| `r` | read · 读 | 4 |
| `w` | write · 写 | 2 |
| `x` | execute · 执行 | 1 |

So `640` = owner `rw-` (6), group `r--` (4), others `---` (0). 所以 `640` 表示：所有者读写、组只读、其他人无权限。

### 2.2 Everyday commands | 常用命令

| Task · 任务 | Command · 命令 |
| :-- | :-- |
| Who am I / which groups · 我是谁、属于哪些组 | `whoami`, `id` |
| List files with details · 查看文件详情 | `ls -la` |
| Change permissions · 修改权限 | `chmod 640 file` |
| Change owner · 修改所有者 | `chown user:group file` |
| Find text · 搜索文本 | `grep -r "keyword" dir/` |
| Running processes · 查看进程 | `ps aux`, `top` |
| Listening ports · 监听端口 | `ss -tuln` |
| Read logs · 查看日志 | `less /var/log/syslog`, `journalctl -u ssh` |
| Text pipeline · 文本管道 | `cut`, `sort`, `uniq -c`, `awk` |

### 2.3 Why scripting matters | 为什么要学脚本

Security work is full of repetitive tasks: parsing logs, checking many hosts, comparing hashes. A short script turns an hour of manual work into seconds and makes it repeatable.

安全工作里有大量重复任务：分析日志、检查多台主机、比对哈希。几行脚本就能把一小时的手工操作变成几秒，并且可重复。

## 3. Attack view | 攻击视角（仅限靶场 / lab only）

On Linux, attackers who gain a foothold often look for **misconfiguration** rather than exotic exploits. In concept, they ask:

在 Linux 上，拿到立足点的攻击者往往寻找的是**配置错误**，而不是什么高深漏洞。从概念上，他们会问：

- Are there files or directories with **overly permissive** access (e.g. world-writable scripts run by root)? 是否存在权限**过宽**的文件或目录（比如被 root 执行、却人人可写的脚本）？
- Is `sudo` configured too broadly? `sudo` 是否授权过宽？
- Are there leftover **credentials, keys or tokens** in config files, shell history or logs? 配置文件、历史记录或日志里是否遗留了**口令、密钥或令牌**？
- Are **SUID** binaries or unpatched services present? 是否存在不必要的 **SUID** 程序或未打补丁的服务？

This is why Linux hardening is mostly about removing those openings. I deliberately leave out step-by-step privilege-escalation recipes; practise legally in the labs in section 5.

所以 Linux 加固的核心，就是把这些缺口堵上。我刻意不写分步骤的提权流程，请在第 5 节的合法靶场里练习。

## 4. Defense view | 防御视角

| Area · 方面 | Good practice · 良好实践 |
| :-- | :-- |
| **Least privilege** · 最小权限 | Use normal accounts; grant `sudo` narrowly; no shared root logins. 平时用普通账号，`sudo` 精细授权，不共享 root。 |
| **File permissions** · 文件权限 | Keep secrets at `600`/`640`; avoid world-writable files. 敏感文件设为 `600`/`640`，避免全局可写。 |
| **SSH hardening** · SSH 加固 | Key-based login, disable password login and direct root login. 使用密钥登录，禁用密码登录和 root 直接登录。 |
| **Updates** · 更新 | Patch the OS and packages regularly. 定期更新系统和软件包。 |
| **Minimal services** · 最小服务 | Remove or disable what you do not use. 删除或停用不用的服务。 |
| **Secrets handling** · 机密管理 | Never hard-code passwords in scripts; use env vars or a secrets manager. 不要在脚本里硬编码口令，使用环境变量或机密管理工具。 |
| **Logging & audit** · 日志与审计 | Keep auth logs, review failed logins, use `auditd` where needed. 保留认证日志，检查失败登录，必要时使用 `auditd`。 |

A simple **defensive audit** on your own machine, listing files with the SUID bit so you can review whether each one is expected:

在你**自己的机器**上做一次简单的**防御性审计**：列出带 SUID 位的文件，逐一确认是否合理：

```bash
find / -perm -4000 -type f 2>/dev/null
```

## 5. Lab / Practice | 实操（合法环境）

Use your own Linux machine, a VM (VirtualBox / VMware), WSL on Windows, or a Docker container. Windows users: `netstat -an` is a rough equivalent of `ss -tuln`.

使用你自己的 Linux 机器、虚拟机、Windows 上的 WSL 或 Docker 容器。Windows 用户可用 `netstat -an` 近似替代 `ss -tuln`。

**Exercise A: count failed SSH logins on your own server | 练习 A：统计自己服务器上的 SSH 登录失败**

Log path and format vary by distro (Debian/Ubuntu: `/var/log/auth.log`; RHEL-family: `/var/log/secure`). 日志路径和格式因发行版而异（Debian/Ubuntu 为 `/var/log/auth.log`，RHEL 系为 `/var/log/secure`）。

```bash
# Bash: top source IPs of failed password attempts
grep "Failed password" /var/log/auth.log \
  | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn | head
```

```python
# Python: the same idea, more readable and easier to extend
import re
from collections import Counter

pattern = re.compile(r"Failed password .* from (\d+\.\d+\.\d+\.\d+)")
counts = Counter()

with open("/var/log/auth.log") as f:
    for line in f:
        m = pattern.search(line)
        if m:
            counts[m.group(1)] += 1

for ip, n in counts.most_common(5):
    print(f"{ip:<16} {n} failed attempts")
```

**Exercise B: wargames built for learning | 练习 B：专为学习设计的闯关游戏**

**OverTheWire: Bandit** is a free, legal wargame that teaches Linux command-line skills step by step through SSH. OverTheWire 的 **Bandit** 是免费、合法的闯关游戏，通过 SSH 一步步教你 Linux 命令行。

**My progress / 我的进度**
- [ ] Explain `rwx` and octal modes without looking them up / 不查资料讲清 `rwx` 与数字权限
- [ ] Run the log script on my own VM and read the result / 在自己的虚拟机上运行日志脚本并解读结果
- [ ] Finish the first 10 levels of Bandit / 完成 Bandit 前 10 关
- [ ] Write one small Python tool and publish it here / 写一个小型 Python 工具并发布到本仓库

## 6. References | 参考

- Linux Journey: <https://linuxjourney.com/>
- OverTheWire Bandit: <https://overthewire.org/wargames/bandit/>
- Linux man pages: <https://man7.org/linux/man-pages/>
- GNU Bash manual: <https://www.gnu.org/software/bash/manual/>
- Python documentation: <https://docs.python.org/3/>

---

⬅️ [Back to index | 返回目录](../README.md)
