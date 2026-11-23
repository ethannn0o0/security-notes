# 07 · CTF & Lab Write-ups | CTF 与靶场

> 🏷️ `ctf` `labs` `writeup` `learning` `beginner`  
> 📅 Written: 2026-11-24 · Status: first draft / 初稿

> ⚠️ For legal, authorized learning only. 仅用于合法授权的学习。

---

## 1. Concept | 概念

A **CTF (Capture The Flag)** is a security competition or practice challenge in which you solve puzzles to find a hidden string called a **flag** (for example `flag{...}`). Labs and wargames work the same way, but are available any time. They are the safest and most legal way to practise real techniques, because the targets are **built to be attacked**.

**CTF（夺旗赛）**是一种安全竞赛或练习：通过解题找到一串隐藏的字符串，称为 **flag**（例如 `flag{...}`）。靶场和闯关游戏形式相同，但随时可玩。它们是练习真实技术最安全、最合法的方式，因为目标本来就是**为被攻击而搭建的**。

| Format · 形式 | How it works · 玩法 |
| :-- | :-- |
| **Jeopardy** · 解题模式 | A board of challenges by category and points; solve in any order. 按类别和分值列出题目，任意顺序解题。 |
| **Attack-Defense** · 攻防模式 | Teams defend their own services while attacking others'. 各队一边防守自己的服务，一边攻击他队的服务。 |
| **King of the Hill** · 山丘之王 | Teams fight to control a shared target. 各队争夺同一目标的控制权。 |
| **Wargame / Lab** · 闯关与靶场 | Always-on levels or machines to learn at your own pace. 常驻的关卡或靶机，按自己的节奏学习。 |

## 2. How it works | 原理

### 2.1 Challenge categories | 题目类别

| Category · 类别 | What you practise · 练什么 | Connects to my notes · 对应笔记 |
| :-- | :-- | :-- |
| **Web** · Web | Finding flaws in web apps. 发现 Web 应用中的缺陷。 | [01 · OWASP Top 10](01-web-security-owasp-top10.md) |
| **Crypto** · 密码学 | Spotting weak or misused cryptography. 识别弱加密或错误使用。 | [06 · Cryptography](06-cryptography.md) |
| **Forensics** · 取证 | Analyzing files, memory, and network captures. 分析文件、内存和流量捕获。 | [02 · Network](02-network-security-basics.md) |
| **Reverse engineering** · 逆向 | Understanding what a program does without source. 在没有源码时理解程序行为。 | Linux & tooling · [03](03-linux-and-scripting.md) |
| **Pwn / binary exploitation** · 二进制利用 | Memory-safety bugs in compiled programs. 编译程序中的内存安全问题。 | Advanced; later. 进阶，后续再学。 |
| **OSINT** · 开源情报 | Finding information from public sources, **within the rules**. 在**规则范围内**从公开信息中找线索。 | Recon phase · [04](04-pentest-methodology.md) |
| **Misc** · 杂项 | Encodings, puzzles, scripting. 编码、谜题、脚本。 | [03 · Scripting](03-linux-and-scripting.md) |

### 2.2 A solving workflow | 解题流程

```text
1. Read   →  2. Recon  →  3. Hypothesize  →  4. Test  →  5. Capture flag  →  6. Write up
   读题        观察        提出假设            验证        拿到 flag          复盘记录
```

1. **Read** the statement and any hints carefully. 仔细读题和提示。
2. **Recon**: inspect the files, the page, the service; note what you see. 观察文件、页面、服务，并记录所见。
3. **Hypothesize**: *"What is this challenge trying to teach?"* 提出假设：*"这道题想教我什么？"*
4. **Test** one idea at a time; keep notes of what failed. 一次只验证一个想法，记录失败的尝试。
5. **Capture the flag** and submit it on the platform. 拿到 flag 并提交到平台。
6. **Write up** what you did and learned. 写下过程和收获。

### 2.3 A tiny illustrative example | 一个小型示意例子

> This is a **toy example I made up to show the workflow**. It is not from a real competition. 这是我为演示流程**自编的玩具例子**，并非来自真实比赛。

**Challenge · 题目:** *"The flag is hidden in this string: `SGFja2VyIEV0aGlj`"*

**Recon · 观察:** it only uses letters and digits and has a length that is a multiple of 4. That is typical of **Base64**. 只含字母和数字，长度是 4 的倍数，这是 **Base64** 的典型特征。

```bash
echo SGFja2VyIEV0aGlj | base64 -d
# Hacker Ethic
```

**Lesson · 收获:** encoding is **not** protection (see note 06). 编码**不是**保护（见笔记 06）。

### 2.4 Common tool categories | 常用工具类别

| Need · 需求 | Examples · 例子 |
| :-- | :-- |
| Decode / transform data · 解码与转换 | CyberChef, `base64`, `xxd` |
| Inspect files · 检查文件 | `file`, `strings`, `exiftool`, `binwalk` |
| Network captures · 流量分析 | Wireshark |
| Web testing (own/lab targets) · Web 测试（自己或靶场目标） | Browser dev tools, Burp Suite |
| Reverse engineering · 逆向 | Ghidra |
| Scripting · 脚本 | Python, Bash |

## 3. Attack view | 攻击视角（仅限靶场 / lab only）

CTFs teach the **attacker's way of thinking** in a safe sandbox: look for assumptions that can be broken, combine small clues, and automate repetitive work. The skill that transfers best to real security work is not any single trick; it is **patient, systematic investigation**.

CTF 在安全的沙盒里训练的是**攻击者的思维方式**：寻找可以被打破的假设，把零散线索组合起来，把重复劳动自动化。最能迁移到真实安全工作的不是某个技巧，而是**耐心、系统的排查能力**。

> 🚫 **Stay inside the rules.** Attack only the challenge targets you are given. Never attack the competition platform itself, other participants, or any system outside the stated scope.  
> 🚫 **守规矩。** 只攻击题目给定的目标。绝不攻击比赛平台本身、其他参赛者或范围之外的任何系统。

## 4. Defense view | 防御视角

Every category is a defensive lesson in disguise:

每个类别其实都是一堂防御课：

| Category · 类别 | Defensive lesson · 防御启示 |
| :-- | :-- |
| Web | Validate input, enforce access control on the server. 校验输入，在服务端强制访问控制。 |
| Crypto | Use vetted algorithms and libraries correctly. 正确使用成熟算法和库。 |
| Forensics | Keep logs and evidence; know what your systems record. 保留日志和证据，清楚系统记录了什么。 |
| Reverse | Assume attackers can read your binaries; never hide secrets in them. 假设攻击者能读到你的程序，绝不在里面藏机密。 |
| Pwn | Prefer memory-safe languages; enable OS protections; patch. 优先用内存安全语言，启用系统防护，及时打补丁。 |
| OSINT | Review what your organization leaks publicly. 检查组织在公开渠道泄露了什么。 |

**Good habits | 良好习惯:**

- 📓 Take notes **as you go**; they become your write-up. 边做边记，笔记就是你的 Write-up。
- 📜 **Read each platform's rules** on sharing solutions. Many sites only allow write-ups for **retired** challenges; during a **live** competition do not share flags or solutions. 阅读各平台关于分享答案的规定。许多平台只允许发布**已下线**题目的 Write-up；**正在进行**的比赛中不要分享 flag 或解法。
- 🤝 Credit hints, tools and people who helped you. 注明帮助过你的提示、工具和人。

## 5. Lab / Practice | 实操（合法环境）

Beginner-friendly places to start, all built for legal practice:

适合入门、都为合法练习而建的平台：

| Platform · 平台 | Good for · 适合 |
| :-- | :-- |
| **picoCTF** | Beginner CTF with a gentle learning curve. 入门 CTF，难度曲线平缓。 |
| **OverTheWire (Bandit)** | Linux command line through wargame levels. 通过闯关学 Linux 命令行。 |
| **TryHackMe** | Guided rooms and learning paths. 有引导的房间和学习路径。 |
| **Hack The Box** | Machines and challenges; more advanced. 靶机和挑战，难度更高。 |
| **PortSwigger Web Security Academy** | Web security labs. Web 安全靶场。 |
| **CryptoHack** | Cryptography challenges. 密码学挑战。 |
| **CTFtime** | Calendar and team rankings of real CTFs. 真实 CTF 的日历和战队排名。 |

### Write-up template | Write-up 模板

Copy this for every challenge you solve:

每解出一道题就复制一份：

```markdown
# Challenge name | 题目名
- **Platform / Event:**  (e.g. picoCTF 20XX)
- **Category / Points:**  (e.g. Crypto, 100)
- **Date:**  YYYY-MM-DD
- **Difficulty (my view):**  easy / medium / hard

## 1. Challenge | 题目描述
What was given? 题目给了什么？

## 2. Recon | 观察
What did I notice first? 我最先注意到什么？

## 3. Approach | 思路与过程
Hypotheses I tried, including the ones that failed. 我尝试的假设，包括失败的。

## 4. Solution | 解法
The steps that worked (no live-competition flags). 有效的步骤（不含进行中比赛的 flag）。

## 5. Lessons & defense | 收获与防御
What did I learn? How would a defender prevent this? 我学到了什么？防御者如何避免？

## 6. References | 参考
```

**My progress / 我的进度**
- [ ] Finish Bandit levels 0 to 10 / 完成 Bandit 0 到 10 关
- [ ] Solve 5 beginner challenges on picoCTF / 在 picoCTF 解出 5 道入门题
- [ ] Write my first real write-up using the template / 用模板写出第一份真实 Write-up
- [ ] Play in one live CTF with a team or solo / 参加一场实时 CTF（组队或单人）

## 6. References | 参考

- picoCTF: <https://picoctf.org/>
- CTFtime: <https://ctftime.org/>
- CTF101: <https://ctf101.org/>
- CyberChef: <https://gchq.github.io/CyberChef/>
- pwn.college: <https://pwn.college/>
- Root Me: <https://www.root-me.org/>

---

⬅️ [Back to index | 返回目录](../README.md)
