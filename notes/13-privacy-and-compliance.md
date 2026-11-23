# 13 · Privacy & Compliance | 隐私与合规

> 🏷️ `privacy` `compliance` `iso27001` `essential-eight` `nist-csf` `beginner`  
> 📅 Written: 2026-11-24 · Status: first draft / 初稿

> ⚠️ **General orientation only, not legal advice.** Laws and standards change and differ by country and sector. Always check the current text from the **official source** and ask a qualified professional before relying on it. **仅为一般性介绍，不构成法律意见。** 法律和标准会变化，且因国家和行业而异。使用前请查阅**官方来源**的现行文本，并咨询合格的专业人士。

---

## 1. Concept | 概念

**Security** protects data from unauthorized access, change and loss. **Privacy** is about the **right use** of personal information: what is collected, why, who can see it, and how long it is kept. You can have security without privacy (a well-protected database that collects far too much), but not privacy without security. **Compliance** means meeting the legal and standard requirements that apply to you, and being able to **show** that you do.

**安全**保护数据不被未授权访问、篡改和丢失。**隐私**关注个人信息的**正当使用**：收集了什么、为什么收集、谁能看到、保存多久。可以有安全而无隐私（防护良好但收集过多的数据库），但没有安全就谈不上隐私。**合规**是指满足适用于你的法律和标准要求，并且能够**证明**自己做到了。

| Common privacy principles · 常见隐私原则 | Meaning · 含义 |
| :-- | :-- |
| **Data minimization** · 数据最小化 | Collect only what you need. 只收集需要的。 |
| **Purpose limitation** · 目的限制 | Use data only for the stated purpose. 只用于声明的目的。 |
| **Transparency & consent** · 透明与同意 | Tell people what you do and get consent where required. 告知人们你在做什么，必要时取得同意。 |
| **Security safeguards** · 安全保障 | Protect data appropriately. 采取适当措施保护数据。 |
| **Retention limits** · 保存期限 | Delete data when no longer needed. 不再需要时删除。 |
| **Individual rights** · 个人权利 | Access, correction, and in some laws deletion. 查阅、更正，部分法律下还包括删除。 |

## 2. How it works | 原理

### 2.1 Frameworks and standards | 框架与标准

| Name · 名称 | What it is · 是什么 |
| :-- | :-- |
| **ISO/IEC 27001** | An international standard for an **Information Security Management System (ISMS)**; organizations can be certified. 信息安全管理体系（ISMS）国际标准，组织可以申请认证。 |
| **NIST Cybersecurity Framework (CSF) 2.0** | Six functions: **Govern, Identify, Protect, Detect, Respond, Recover**. 六大职能：治理、识别、保护、检测、响应、恢复。 |
| **CIS Critical Security Controls** | A prioritized list of practical safeguards. 一份有优先级的实用防护措施清单。 |
| **SOC 2** | An audit report on a service provider's controls. 对服务提供商控制措施的审计报告。 |
| **PCI DSS** | Rules for organizations handling payment card data. 处理支付卡数据的组织需遵守的规则。 |

### 2.2 Laws relevant to me (Australia 🇦🇺 and Malaysia 🇲🇾) | 与我相关的法律（澳大利亚和马来西亚）

| Region · 地区 | Law or guidance · 法律或指引 | Short idea · 简述 |
| :-- | :-- | :-- |
| 🇦🇺 | **Privacy Act 1988 (Cth)** and the **Australian Privacy Principles (APPs)** | Rules for how covered organizations handle personal information. 受规范的组织如何处理个人信息。 |
| 🇦🇺 | **Notifiable Data Breaches (NDB) scheme** | Eligible breaches likely to cause serious harm must be assessed and notified (to the OAIC and affected people). 可能造成严重伤害的符合条件的数据泄露须评估并通报（向 OAIC 和受影响者）。 |
| 🇦🇺 | **Essential Eight** (Australian Signals Directorate / ACSC) | Eight baseline mitigation strategies, see below. 八项基线缓解策略，见下文。 |
| 🇲🇾 | **Personal Data Protection Act 2010 (PDPA)** | Malaysia's main personal data law; it has been **amended** (check the current status). 马来西亚主要的个人数据法律，已有**修订**（请核对最新状态）。 |
| 🇲🇾 | **Cyber Security Act 2024** | A newer law on national cyber security; check scope and current status. 较新的国家网络安全法律；请核对适用范围和最新状态。 |
| 🇪🇺 | **GDPR** | Can apply to organizations outside the EU that handle EU residents' data. 处理欧盟居民数据的欧盟境外组织也可能适用。 |

> 📌 I wrote the entries above from general knowledge. Dates, thresholds and scope change; verify each on the official site (see references).  
> 📌 以上条目凭一般性知识整理，日期、门槛和适用范围会变化，请到官方网站逐项核对（见参考）。

### 2.3 The Essential Eight | Essential Eight（八项基本策略）

| # | Strategy · 策略 |
| :-: | :-- |
| 1 | Application control · 应用程序控制 |
| 2 | Patch applications · 应用程序打补丁 |
| 3 | Configure Microsoft Office macro settings · 配置 Microsoft Office 宏设置 |
| 4 | User application hardening · 用户应用程序加固 |
| 5 | Restrict administrative privileges · 限制管理员权限 |
| 6 | Patch operating systems · 操作系统打补丁 |
| 7 | Multi-factor authentication · 多因素认证 |
| 8 | Regular backups · 定期备份 |

These map neatly onto topics in my earlier notes (patching, least privilege, MFA, backups, macros and malware).

这些和我之前笔记中的主题（补丁、最小权限、MFA、备份、宏与恶意软件）能很好地对应起来。

## 3. Attack view | 攻击视角（仅限靶场 / lab only）

Privacy failures often come from ordinary mistakes rather than sophisticated attacks. Conceptually:

隐私问题往往来自普通失误，而不是高明的攻击。从概念上：

| How exposure happens · 泄露如何发生 | Example · 例子 |
| :-- | :-- |
| Over-collection · 过度收集 | Storing data nobody needs becomes liability in a breach. 存下没人需要的数据，一旦泄露就成为负担。 |
| Misconfiguration · 配置错误 | Public storage, open databases, verbose logs containing personal data. 公开存储、开放数据库、含个人数据的详细日志。 |
| Weak access control · 访问控制薄弱 | Staff browsing records they have no need to see. 员工查看无需查看的记录。 |
| Third parties · 第三方 | A supplier holding your data is breached. 持有你数据的供应商被攻破。 |
| Poor disposal · 处置不当 | Old devices and backups with data still on them. 仍保存着数据的旧设备和备份。 |

## 4. Defense view | 防御视角

| Practice · 实践 | What it involves · 内容 |
| :-- | :-- |
| **Data inventory / mapping** · 数据清单与映射 | Know what personal data you hold, where, and why. 清楚持有哪些个人数据、在哪里、为什么。 |
| **Classification** · 分级分类 | Label data by sensitivity and protect accordingly. 按敏感度标记数据并相应保护。 |
| **Minimize and delete** · 最小化与删除 | Collect less, set retention periods, delete on schedule. 少收集、设定保存期限、按期删除。 |
| **Access control** · 访问控制 | Least privilege and logging of access to sensitive records. 最小权限，并记录对敏感记录的访问。 |
| **Encryption** · 加密 | At rest and in transit (see note 06). 静态与传输加密（见笔记 06）。 |
| **Privacy impact assessment** · 隐私影响评估 | Review risks before launching a new system that uses personal data. 在上线使用个人数据的新系统前评估风险。 |
| **Vendor management** · 供应商管理 | Check and contractually bind suppliers that handle your data. 审查并以合同约束处理你数据的供应商。 |
| **Breach response plan** · 泄露响应预案 | Know who decides, who notifies, and the legal clocks (see note 05). 明确谁决策、谁通报，以及法定时限（见笔记 05）。 |

Some **notification timelines** differ by law. For example, the GDPR generally requires notifying the regulator within 72 hours of becoming aware of a qualifying breach, and Australia's NDB scheme expects a prompt assessment (generally within 30 days). **Check the current rules for your situation.**

**通报时限**因法律而异。例如 GDPR 通常要求在得知符合条件的泄露后 72 小时内通报监管机构，澳大利亚 NDB 制度要求及时评估（一般在 30 天内）。**请核对适用于你情况的现行规定。**

## 5. Lab / Practice | 实操（合法环境）

1. **Map the data** of a *fictional* app (for example a small shop): list each piece of personal data, why it is collected, where it is stored and how long it is kept. 为一个*虚构*的应用（如小型商店）**做数据映射**：列出每项个人数据、收集原因、存放位置和保存期限。
2. Mark what you could **stop collecting**. 标出哪些可以**不再收集**。
3. Map the **Essential Eight** or **CIS Controls** onto a small home or lab setup and score yourself honestly. 把 **Essential Eight** 或 **CIS Controls** 对应到自己的家庭或实验环境，并如实打分。
4. Write a one-page **breach response outline**: who is told, in what order, within what time. 写一页**泄露响应提纲**：通知谁、顺序如何、时限多长。

**My progress / 我的进度**
- [ ] Explain privacy vs security vs compliance / 讲清隐私、安全、合规的区别
- [ ] Complete a data map for a fictional app / 为虚构应用完成数据映射
- [ ] Self-assess against the Essential Eight / 对照 Essential Eight 自评
- [ ] Read the current OAIC and PDP (Malaysia) guidance and update this note / 阅读 OAIC 和马来西亚个人数据保护部门的最新指引并更新本笔记

## 6. References | 参考

- 🇦🇺 Essential Eight (ACSC): <https://www.cyber.gov.au/resources-business-and-government/essential-cyber-security/essential-eight>
- 🇦🇺 OAIC, Office of the Australian Information Commissioner: <https://www.oaic.gov.au/>
- 🇲🇾 Department of Personal Data Protection (JPDP): <https://www.pdp.gov.my/>
- ISO/IEC 27001: <https://www.iso.org/standard/27001>
- NIST Cybersecurity Framework: <https://www.nist.gov/cyberframework>
- CIS Critical Security Controls: <https://www.cisecurity.org/controls>

---

⬅️ [Back to index | 返回目录](../README.md)
