# 01 · Web Security: OWASP Top 10 | Web 安全：OWASP Top 10 入门

> 🏷️ `web-security` `owasp` `beginner`  
> 📅 Written: 2026-11-24 · Status: first draft / 初稿

> ⚠️ For legal, authorized learning only. 仅用于合法授权的学习。

---

## 1. Concept | 概念

The **OWASP Top 10** is a widely used awareness list of the most critical web application security risks, maintained by the Open Worldwide Application Security Project (OWASP). It is not a checklist of every possible bug; it is a **starting point** for developers and defenders to prioritize.

**OWASP Top 10** 是由 OWASP（开放式 Web 应用程序安全项目）维护的、被广泛使用的 Web 应用最关键安全风险清单。它不是漏洞大全，而是开发者和防御者排定优先级的**起点**。

> 📌 This note follows the **OWASP Top 10:2021** edition. OWASP revises the list periodically, so always check the current edition at <https://owasp.org/Top10/>.  
> 📌 本笔记基于 **OWASP Top 10:2021**。该榜单会定期更新，请以 <https://owasp.org/Top10/> 上的最新版本为准。

## 2. How it works | 原理

Each category groups many concrete weaknesses by root cause. The 2021 list:

2021 版的十个类别如下，每一类按"根因"归并了许多具体漏洞：

| ID | Category · 类别 | One-line idea · 一句话理解 |
| :-: | :-- | :-- |
| A01 | **Broken Access Control** · 失效的访问控制 | Users can act outside their permissions (e.g. view another user's data by changing an ID). 用户越权做了不该做的事。 |
| A02 | **Cryptographic Failures** · 加密机制失效 | Sensitive data is not protected properly in transit or at rest. 敏感数据在传输或存储中保护不当。 |
| A03 | **Injection** · 注入 | Untrusted input is interpreted as code or commands (SQL, OS, etc.). 不可信输入被当作代码或命令执行。 |
| A04 | **Insecure Design** · 不安全的设计 | The flaw is in the design itself, not just the code. 问题出在设计层面，而不仅是代码。 |
| A05 | **Security Misconfiguration** · 安全配置错误 | Default settings, open services, verbose errors. 默认配置、多余服务、过度详细的报错。 |
| A06 | **Vulnerable and Outdated Components** · 易受攻击和过时的组件 | Using libraries or software with known flaws. 使用存在已知漏洞的库或软件。 |
| A07 | **Identification and Authentication Failures** · 身份识别和身份验证失效 | Weak login, session or password handling. 登录、会话或密码处理薄弱。 |
| A08 | **Software and Data Integrity Failures** · 软件和数据完整性失效 | Trusting updates, plugins or data without verifying integrity. 未校验完整性就信任更新、插件或数据。 |
| A09 | **Security Logging and Monitoring Failures** · 安全日志和监控失效 | Attacks go unnoticed because events are not logged or watched. 缺乏日志与监控，攻击无人察觉。 |
| A10 | **Server-Side Request Forgery (SSRF)** · 服务端请求伪造 | The server is tricked into making requests the attacker chooses. 服务器被诱导发起攻击者指定的请求。 |

The common thread: **applications trust input, identity or configuration they should have verified.**

共同主线：**应用信任了本该先验证的输入、身份或配置。**

## 3. Attack view | 攻击视角（仅限靶场 / lab only）

Thinking like an attacker means asking: *"Where does this application trust something it should not?"* Take **A03 Injection** as the classic example. The bug appears when user input is concatenated directly into a query:

站在攻击者角度，核心问题是：*"这个应用在哪里信任了不该信任的东西？"* 以 **A03 注入**为经典例子，问题出在把用户输入直接拼接进查询语句：

```python
# ❌ Vulnerable: input becomes part of the SQL text
query = "SELECT * FROM users WHERE name = '" + name + "'"
cursor.execute(query)
```

Because the database cannot tell where the developer's SQL ends and the user's data begins, crafted input can change the meaning of the query. I deliberately do **not** include working attack strings against real systems here; practice only in the legal labs listed in section 5.

由于数据库无法区分哪部分是开发者写的 SQL、哪部分是用户数据，精心构造的输入就能改变查询含义。我在这里刻意**不**提供针对真实系统的可用攻击载荷，请只在第 5 节列出的合法靶场中练习。

## 4. Defense view | 防御视角

Defense is about removing the unsafe trust. For injection, the fix is to keep **code and data separate**:

防御的本质是消除不安全的信任。对注入来说，关键是让**代码与数据分离**：

```python
# ✅ Safe: parameterized query, input is always treated as data
cursor.execute("SELECT * FROM users WHERE name = %s", (name,))
```

| Category · 类别 | Core defenses · 核心防护 |
| :-- | :-- |
| A01 Access Control | Deny by default; enforce permission checks on the **server** for every request. 默认拒绝；每次请求都在服务端校验权限。 |
| A02 Cryptography | Use TLS everywhere; modern, vetted algorithms; never invent your own crypto. 全程 TLS；使用成熟算法；绝不自创加密。 |
| A03 Injection | Parameterized queries, input validation, least-privilege DB accounts. 参数化查询、输入校验、数据库最小权限。 |
| A04 Insecure Design | Threat modeling and secure design patterns early. 尽早做威胁建模与安全设计。 |
| A05 Misconfiguration | Hardened baselines, remove unused features, minimal error detail. 加固基线、关闭无用功能、精简报错信息。 |
| A06 Components | Inventory dependencies and patch regularly. 维护依赖清单并定期打补丁。 |
| A07 Authentication | Strong password policy, MFA, safe session management, rate limiting. 强密码策略、多因素认证、安全会话管理、限速。 |
| A08 Integrity | Verify signatures and checksums; secure the CI/CD pipeline. 校验签名与哈希；保护 CI/CD 流水线。 |
| A09 Logging | Log security events, protect logs, alert on anomalies. 记录安全事件、保护日志、异常告警。 |
| A10 SSRF | Allow-list outbound destinations; block internal address ranges. 出站目标白名单；阻断内网地址段。 |

## 5. Lab / Practice | 实操（合法环境）

Only practice on systems you **own** or have **explicit permission** to test. Safe options:

只在你**自己拥有**或**已获明确授权**的系统上练习。推荐的安全环境：

- 🧃 **OWASP Juice Shop** — an intentionally vulnerable app you run locally. 本地运行的故意带漏洞的练习应用。
- 🕸️ **PortSwigger Web Security Academy** — free, hosted labs built for legal practice. 免费、专为合法练习搭建的在线靶场。
- 🐳 A local **Docker** lab on your own machine. 在自己电脑上用 Docker 搭建的本地实验环境。

**My progress / 我的进度**
- [ ] Run a vulnerable lab locally / 在本地跑起一个靶场
- [ ] Reproduce and **fix** one injection bug in my own demo code / 在自己的示例代码里复现并**修复**一个注入问题
- [ ] Write up what I learned / 写下学习总结

## 6. References | 参考

- OWASP Top 10: <https://owasp.org/Top10/>
- OWASP Cheat Sheet Series: <https://cheatsheetseries.owasp.org/>
- OWASP Juice Shop: <https://owasp.org/www-project-juice-shop/>
- PortSwigger Web Security Academy: <https://portswigger.net/web-security>

---

⬅️ [Back to index | 返回目录](../README.md)
