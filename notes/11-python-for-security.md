# 11 · Python for Security | 用 Python 写安全小工具

> 🏷️ `python` `automation` `scripting` `defense` `beginner`  
> 📅 Written: 2026-11-24 · Status: first draft / 初稿

> ⚠️ For legal, authorized learning only. Run these tools only on systems you own or are authorized to test. 仅用于合法授权的学习。这些工具只能用于你自己拥有或已获授权测试的系统。

---

## 1. Concept | 概念

Security work is full of repetitive tasks: checking many files, reading long logs, verifying many hosts. **Python** is a popular choice because it is readable, has a huge standard library and many security-related packages. Writing your own small tools also teaches you **how the protocols and formats really work**.

安全工作里有大量重复任务：检查很多文件、阅读很长的日志、核对很多主机。**Python** 很受欢迎，因为它易读、标准库丰富、安全相关的第三方库也很多。自己写小工具，还能让你真正理解**协议和格式是如何工作的**。

| Library · 库 | Typical use · 典型用途 |
| :-- | :-- |
| `hashlib` | Hashes and file integrity. 哈希与文件完整性。 |
| `socket` | Basic network connections. 基础网络连接。 |
| `re` | Pattern matching in logs. 日志中的模式匹配。 |
| `argparse` | Command-line options. 命令行参数。 |
| `requests` | HTTP requests and APIs. HTTP 请求与 API。 |
| `cryptography` | Vetted encryption primitives (see note 06). 经过审查的加密原语（见笔记 06）。 |
| `scapy` | Packet crafting and parsing in your own lab. 在自己的实验环境里构造与解析数据包。 |

## 2. How it works | 原理

Three small, **defensive** tools. Each one is useful on its own and builds a skill.

三个小而**偏防御**的工具，每个都有实际用处，也能练出一项技能。

### 2.1 File integrity checker | 文件完整性校验

Compute a SHA-256 hash and compare it to a published value, so you know a download was not corrupted or tampered with. Reading in chunks keeps memory use low for large files.

计算 SHA-256 并与官方公布的值对比，确认下载文件没有损坏或被篡改。分块读取让大文件也不会占用太多内存。

```python
import hashlib
import sys

def sha256_file(path, chunk_size=65536):
    h = hashlib.sha256()
    with open(path, "rb") as f:
        while chunk := f.read(chunk_size):
            h.update(chunk)
    return h.hexdigest()

if __name__ == "__main__":
    print(sha256_file(sys.argv[1]))
```

### 2.2 Simple TCP port check (own hosts only) | 简单的 TCP 端口检查（仅限自己的主机）

Checks whether a few ports on **your own machine or lab VM** accept connections: a quick way to confirm that unused services are really closed.

检查**你自己的机器或实验虚拟机**上的几个端口是否接受连接，可以快速确认不用的服务确实已关闭。

```python
import socket

def is_open(host, port, timeout=1.0):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.settimeout(timeout)
        return s.connect_ex((host, port)) == 0

HOST = "127.0.0.1"   # only systems you own or may test
for port in (22, 80, 443):
    print(port, "open" if is_open(HOST, port) else "closed")
```

### 2.3 Password exposure check with k-anonymity | 用 k-匿名检查口令是否泄露

The *Have I Been Pwned* range API lets you check a password **without sending it**: you send only the **first 5 characters of its SHA-1 hash** and compare the rest locally. SHA-1 is used here only because that API requires it; it is **not** a way to store passwords (see note 06).

*Have I Been Pwned* 的 range API 允许你在**不发送口令本身**的情况下检查：只发送其 **SHA-1 哈希的前 5 个字符**，其余部分在本地比对。这里使用 SHA-1 仅因为该 API 要求，**不是**存储口令的方式（见笔记 06）。

```python
import hashlib
import requests  # pip install requests

def pwned_count(password):
    sha1 = hashlib.sha1(password.encode()).hexdigest().upper()
    prefix, suffix = sha1[:5], sha1[5:]
    r = requests.get(f"https://api.pwnedpasswords.com/range/{prefix}", timeout=10)
    r.raise_for_status()
    for line in r.text.splitlines():
        hash_suffix, count = line.split(":")
        if hash_suffix == suffix:
            return int(count)
    return 0
```

## 3. Attack view | 攻击视角（仅限靶场 / lab only）

Scripting lowers the **cost and effort** of attacks: guessing credentials, scanning widely, or sending bulk phishing can all be automated. That is the main reason defenders must expect **high-volume, automated** activity and design for it.

脚本降低了攻击的**成本和门槛**：猜测凭据、大范围扫描、批量钓鱼都可以自动化。这正是防御者必须预期**高频、自动化**行为并据此设计的原因。

| Automated activity · 自动化行为 | Defender response · 防御对策 |
| :-- | :-- |
| Repeated login attempts · 反复尝试登录 | Rate limiting, lockout, MFA, alerting. 限速、锁定、多因素认证、告警。 |
| Wide network scanning · 大范围扫描 | Close unused ports; detect and block scan patterns. 关闭无用端口；检测并阻断扫描特征。 |
| Scraping and abuse of APIs · 抓取与滥用 API | Authentication, quotas, bot detection. 认证、配额、机器人检测。 |

I do not publish tools for attacking systems you do not own.

我不会发布用于攻击他人系统的工具。

## 4. Defense view | 防御视角

Good habits for writing security tools:

编写安全工具的良好习惯：

| Habit · 习惯 | Why · 原因 |
| :-- | :-- |
| **Authorization first** · 先有授权 | Every target must be yours or explicitly allowed. 每个目标都必须是你自己的或明确获准的。 |
| **No hard-coded secrets** · 不写死机密 | Read keys from environment variables or a secrets manager. 从环境变量或机密管理器读取密钥。 |
| **Timeouts and error handling** · 超时与异常处理 | Network code must not hang or crash on bad input. 网络代码不能因异常输入卡死或崩溃。 |
| **Rate limits and politeness** · 限速与克制 | Do not overload services, even your own. 不要压垮服务，即使是你自己的。 |
| **Validate input** · 校验输入 | Never trust file content, API replies or command arguments blindly. 不要盲目信任文件内容、API 响应或命令行参数。 |
| **Logging** · 日志 | Record what the tool did and when. 记录工具做了什么、何时做的。 |
| **Virtual environments and pinned versions** · 虚拟环境与固定版本 | Reproducible, and lowers supply-chain risk. 可复现，并降低供应链风险。 |
| **Tests** · 测试 | Verify tools on known-good data first. 先在已知数据上验证工具。 |

## 5. Lab / Practice | 实操（合法环境）

```bash
# Create an isolated environment for your tools
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install requests
```

1. Build the **three tools** above, add `argparse` so they take a path / host as an option. 实现上面三个工具，并用 `argparse` 让它们能通过参数接收路径或主机。
2. Test the hash tool on a file and compare with `sha256sum`. 用哈希工具处理一个文件，并与 `sha256sum` 对比。
3. Test the port checker against your own lab VM before and after closing a service. 在关闭某个服务前后，对自己的实验虚拟机运行端口检查。
4. Test the password checker with **made-up test passwords**, not your real ones. 用**自编的测试口令**测试口令检查器，不要用真实口令。
5. Put the finished scripts in a `tools/` folder of this repository with a short README. 把完成的脚本放进本仓库的 `tools/` 文件夹，并附上简短 README。

Practice | 练习：**OverTheWire Bandit** (scripting at the shell), **Python Challenge**, **picoCTF** scripting tasks.

**My progress / 我的进度**
- [ ] Write and test the file integrity checker / 编写并测试文件完整性校验工具
- [ ] Write and test the port checker on my lab VM / 编写并在实验虚拟机上测试端口检查工具
- [ ] Add `argparse` and error handling to both / 为两个工具添加 `argparse` 与异常处理
- [ ] Publish my first tool in a `tools/` folder / 在 `tools/` 文件夹发布我的第一个工具

## 6. References | 参考

- Python documentation: <https://docs.python.org/3/>
- Python `socket` module: <https://docs.python.org/3/library/socket.html>
- Have I Been Pwned, Pwned Passwords API: <https://haveibeenpwned.com/API/v3#PwnedPasswords>
- Scapy: <https://scapy.net/>
- OWASP Cheat Sheet Series: <https://cheatsheetseries.owasp.org/>

---

⬅️ [Back to index | 返回目录](../README.md)
