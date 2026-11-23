# 09 · Cloud Security Basics | 云安全基础

> 🏷️ `cloud` `iam` `aws` `azure` `gcp` `beginner`  
> 📅 Written: 2026-11-24 · Status: first draft / 初稿

> ⚠️ For legal, authorized learning only. 仅用于合法授权的学习。

---

## 1. Concept | 概念

Moving to the cloud does not remove security work; it **changes who does which part**. Most real-world cloud incidents come from **misconfiguration and leaked credentials**, not from the cloud provider being "hacked".

上云并不会让安全工作消失，而是**改变了谁负责哪一部分**。现实中大多数云安全事件源于**配置错误和凭据泄露**，而不是云厂商被"黑"。

### The shared responsibility model | 共享责任模型

| Layer · 层面 | Provider · 云厂商 | Customer · 客户 |
| :-- | :-: | :-: |
| Physical data centers, hardware · 机房与硬件 | ✅ | |
| Virtualization, core cloud services · 虚拟化与核心服务 | ✅ | |
| OS, patches (in IaaS) · 操作系统与补丁（IaaS） | | ✅ |
| Network rules, firewalls · 网络规则与防火墙 | | ✅ |
| Identity and access (IAM) · 身份与访问 | | ✅ |
| Your data and its protection · 你的数据及其保护 | | ✅ |

The exact split shifts between **IaaS**, **PaaS** and **SaaS**, but **your data and your access settings are always your responsibility**.

具体划分在 **IaaS**、**PaaS**、**SaaS** 之间有所不同，但**你的数据和访问设置永远由你负责**。

## 2. How it works | 原理

### 2.1 Identity is the new perimeter | 身份是新的边界

In the cloud, almost everything is controlled by **API calls authorized by identities** (users, roles, service accounts). Whoever holds a powerful credential holds the environment.

在云上，几乎一切都由**经身份授权的 API 调用**控制（用户、角色、服务账号）。谁掌握了高权限凭据，谁就掌握了整个环境。

```json
// ❌ Too broad: allows every action on every resource
{ "Effect": "Allow", "Action": "*", "Resource": "*" }

// ✅ Least privilege: read-only on one folder of one bucket
{
  "Effect": "Allow",
  "Action": ["s3:GetObject"],
  "Resource": "arn:aws:s3:::my-app-bucket/reports/*"
}
```

### 2.2 Key building blocks | 关键组成

| Area · 方面 | What it means · 含义 |
| :-- | :-- |
| **IAM** · 身份与访问管理 | Users, roles, policies, MFA, short-lived credentials. 用户、角色、策略、多因素认证、短期凭据。 |
| **Network** · 网络 | Virtual networks, subnets, security groups, private endpoints. 虚拟网络、子网、安全组、私有端点。 |
| **Storage** · 存储 | Buckets and disks; public vs private; encryption. 存储桶与磁盘；公开与私有；加密。 |
| **Keys & secrets** · 密钥与机密 | Key management service (KMS) and secrets managers. 密钥管理服务（KMS）与机密管理器。 |
| **Logging** · 日志 | Audit trails of API activity (e.g. CloudTrail, Azure Activity Log, GCP Cloud Audit Logs). API 活动审计日志。 |
| **Infrastructure as Code** · 基础设施即代码 | Templates (e.g. Terraform) that can be reviewed and scanned. 可审查、可扫描的模板（如 Terraform）。 |

## 3. Attack view | 攻击视角（仅限靶场 / lab only）

Common cloud weaknesses, described conceptually:

从概念上看，常见的云上弱点：

| Weakness · 弱点 | Why it matters · 为什么重要 |
| :-- | :-- |
| Publicly readable storage buckets · 公开可读的存储桶 | Sensitive files exposed to anyone with the link. 敏感文件对任何知道链接的人开放。 |
| Access keys committed to code repositories · 访问密钥提交进代码仓库 | Leaked keys give direct API access. 泄露的密钥直接带来 API 访问权限。 |
| Over-permissive IAM policies · 过宽的 IAM 策略 | One compromised identity can reach everything. 一个被攻破的身份就能触达所有资源。 |
| Open management ports / security groups · 对外开放的管理端口与安全组 | Remote access reachable from the whole internet. 远程管理端口暴露给整个互联网。 |
| Server-side request forgery to the metadata service · 服务端请求伪造访问元数据服务 | A web flaw can expose instance credentials. Web 漏洞可能暴露实例凭据。 |
| No logging or alerts · 没有日志与告警 | Misuse goes unnoticed. 滥用行为无人察觉。 |

I do not describe exploitation steps against real cloud accounts. Practise only in your own account or purpose-built training environments.

这里不描述针对真实云账号的利用步骤。请只在你自己的账号或专门的训练环境中练习。

## 4. Defense view | 防御视角

| Area · 方面 | Good practice · 良好实践 |
| :-- | :-- |
| **Root / admin account** · 根账号 | Enable MFA, do not use it daily, no access keys for it. 开启多因素认证，不日常使用，不为其创建访问密钥。 |
| **Least privilege** · 最小权限 | Narrow policies; prefer roles with **short-lived** credentials over long-lived keys. 收窄策略；优先使用带**短期**凭据的角色，而不是长期密钥。 |
| **Storage** · 存储 | Block public access by default; encrypt at rest and in transit. 默认阻止公开访问；静态与传输加密。 |
| **Secrets** · 机密 | Use a secrets manager; scan repositories for leaked keys. 使用机密管理器；扫描仓库中的泄露密钥。 |
| **Network** · 网络 | Default-deny security groups; avoid exposing SSH/RDP to the internet. 默认拒绝的安全组；不要把 SSH/RDP 暴露到公网。 |
| **Metadata service** · 元数据服务 | Use the hardened version (e.g. AWS IMDSv2). 使用加固版本（如 AWS IMDSv2）。 |
| **Logging & alerts** · 日志与告警 | Turn on audit logs in all regions; alert on risky changes. 在所有区域开启审计日志；对高风险变更告警。 |
| **Posture checks** · 配置检查 | Use CSPM tools and benchmarks (e.g. CIS) to find misconfigurations. 使用 CSPM 工具和基线（如 CIS）发现配置错误。 |
| **Cost safety** · 成本防护 | Set billing alerts; leaked keys are often used to run costly resources. 设置账单告警；泄露的密钥常被用来运行昂贵资源。 |

## 5. Lab / Practice | 实操（合法环境）

1. Create a **free-tier account of your own** with a fresh email, enable **MFA**, and set a **billing alert** first. 用新邮箱创建**自己的免费层账号**，先开启 **MFA** 并设置**账单告警**。
2. Create a private storage bucket, confirm that **public access is blocked**, then upload a harmless test file. 创建一个私有存储桶，确认**公开访问已被阻止**，再上传无害的测试文件。
3. Create a user or role with a **read-only** policy for that one bucket and test that it cannot do anything else. 为该存储桶创建仅有**只读**权限的用户或角色，并测试它做不了其他事。
4. Turn on audit logging and find your own actions in the log. 开启审计日志，并在日志中找到自己的操作。
5. **Delete everything** when finished to avoid charges. 完成后**删除所有资源**，避免产生费用。

Learning environments | 学习环境：**flAWS.cloud** (a free, intentionally flawed training site) and your own sandbox account. Tools that deploy vulnerable infrastructure (such as CloudGoat) must be run **only in an isolated account of your own**, and they can cost money.

**flAWS.cloud**（免费、故意带缺陷的训练站点）和你自己的沙盒账号。会部署漏洞基础设施的工具（如 CloudGoat）只能在**你自己专用的隔离账号**中运行，并且可能产生费用。

**My progress / 我的进度**
- [ ] Explain the shared responsibility model with an example / 举例讲清共享责任模型
- [ ] Set up my own account with MFA and billing alerts / 搭建带 MFA 与账单告警的个人账号
- [ ] Write one least-privilege policy and test it / 编写并测试一条最小权限策略
- [ ] Finish the flAWS.cloud levels / 完成 flAWS.cloud 的关卡

## 6. References | 参考

- AWS shared responsibility model: <https://aws.amazon.com/compliance/shared-responsibility-model/>
- Microsoft Azure security documentation: <https://learn.microsoft.com/azure/security/>
- Google Cloud security: <https://cloud.google.com/security>
- flAWS.cloud: <http://flaws.cloud/>
- Cloud Security Alliance: <https://cloudsecurityalliance.org/>

---

⬅️ [Back to index | 返回目录](../README.md)
