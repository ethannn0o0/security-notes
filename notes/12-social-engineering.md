# 12 · Social Engineering & Security Awareness | 社会工程与安全意识

> 🏷️ `social-engineering` `phishing` `awareness` `email-security` `beginner`  
> 📅 Written: 2026-11-24 · Status: first draft / 初稿

> ⚠️ For legal, authorized learning only. This note is about **recognizing and defending against** manipulation. It contains no phishing templates or scripts. 仅用于合法授权的学习。本笔记只讲**识别与防范**，不包含任何钓鱼模板或话术。

---

## 1. Concept | 概念

**Social engineering** manipulates **people** instead of breaking technology: it persuades someone to hand over information, click a link, or approve something they should not. It works because technical defenses are often strong while humans are busy, trusting and helpful. A large share of real breaches begin with a **phishing** message.

**社会工程**操纵的是**人**而不是技术：说服某人交出信息、点击链接或批准不该批准的事。它之所以有效，是因为技术防线往往很强，而人却忙碌、信任他人、乐于助人。大量真实入侵都始于一封**钓鱼**消息。

| Type · 类型 | Short description · 简述 |
| :-- | :-- |
| **Phishing** · 钓鱼邮件 | Mass messages pretending to be a trusted sender. 冒充可信发件人的批量消息。 |
| **Spear phishing** · 鱼叉式钓鱼 | Targeted at a specific person using personal details. 针对特定个人、利用其个人信息。 |
| **Whaling** · 捕鲸 | Spear phishing aimed at executives. 针对高管的鱼叉式钓鱼。 |
| **Smishing / Vishing** · 短信 / 电话钓鱼 | Phishing by SMS / by phone call. 通过短信 / 电话进行的钓鱼。 |
| **Business Email Compromise (BEC)** · 商务邮件欺诈 | Fake or hijacked email asking to change payment details. 伪造或被劫持的邮件，要求变更付款信息。 |
| **Pretexting** · 借口诈骗 | An invented scenario to extract information. 编造情境以套取信息。 |
| **Baiting** · 诱饵 | Leaving tempting items such as infected USB drives. 投放诱人的物品，如带毒 U 盘。 |
| **Tailgating** · 尾随 | Following someone into a secured area. 尾随他人进入受限区域。 |

## 2. How it works | 原理

### 2.1 The psychology | 心理学原理

Manipulation usually leans on a few well-known pressures:

操纵通常利用几种常见的心理压力：

| Lever · 杠杆 | What it feels like · 感觉像什么 |
| :-- | :-- |
| **Authority** · 权威 | "This is the CEO / the bank / IT support." 「我是 CEO / 银行 / IT 支持」。 |
| **Urgency** · 紧迫 | "Act in 30 minutes or your account is closed." 「30 分钟内处理，否则账号将被关闭」。 |
| **Fear** · 恐惧 | "Suspicious activity, your data is at risk." 「检测到异常，你的数据有风险」。 |
| **Reward / Greed** · 奖励 | "You won a prize, claim now." 「你中奖了，立即领取」。 |
| **Trust / Reciprocity** · 信任 / 互惠 | A friendly favour before the real request. 提出真正请求前先帮个小忙。 |
| **Social proof** · 从众 | "Everyone else has already done it." 「大家都已经这么做了」。 |

The common pattern: **emotion first, thinking second.** Slowing down is the best counter.

共同模式：**先激起情绪，后才思考。** 放慢节奏就是最好的反制。

### 2.2 How email authentication helps | 邮件认证如何帮忙

| Mechanism · 机制 | What it checks · 检查什么 |
| :-- | :-- |
| **SPF** | Was the mail sent from a server allowed for that domain? 邮件是否来自该域名允许的服务器？ |
| **DKIM** | Is the message signed by the domain and unaltered? 邮件是否由该域名签名且未被篡改？ |
| **DMARC** | Policy that tells receivers what to do when SPF/DKIM fail. 当 SPF/DKIM 失败时，告诉收件方该如何处理的策略。 |

These reduce **spoofing of your own domain**, but they do not stop lookalike domains or compromised real accounts, so people still matter.

它们能减少**对你自己域名的伪造**，但挡不住外观相似的域名和被攻陷的真实账号，所以人依然重要。

## 3. Attack view | 攻击视角（仅限靶场 / lab only）

Rather than describing how to write lures, here is how attackers **think**, which is what you need to recognize them:

这里不讲如何编写诱饵，而是讲攻击者**怎么想**，这正是你识别他们所需要的：

| Attacker aim · 攻击者目标 | Typical sign for the victim · 受害者常见迹象 |
| :-- | :-- |
| Steal credentials · 窃取凭据 | A link to a login page on an unfamiliar or slightly misspelled domain. 指向陌生或拼写略有偏差域名的登录页链接。 |
| Get malware executed · 让恶意软件运行 | Unexpected attachment, especially asking to enable macros. 意外的附件，尤其是要求启用宏。 |
| Divert money · 转移资金 | Sudden change of bank details, pressure to keep it secret. 突然变更收款账户，并要求保密。 |
| Gather information first · 先收集信息 | Odd questions about staff, systems or schedules. 关于员工、系统或日程的奇怪提问。 |

Attackers collect personal information from public sources to make messages convincing, so **what you post publicly matters**.

攻击者会从公开渠道收集个人信息来让消息更可信，所以**你在公开场合发布的内容很重要**。

## 4. Defense view | 防御视角

### 4.1 A quick red-flag checklist | 快速红旗清单

- ❓ Unexpected message, or unusual request from a known sender? 意外的消息，或熟人提出了不寻常的请求？
- ⏱️ Pressure to act **right now**? 要求**立即**处理？
- 🔗 Does the real link (hover to see it) match the claimed site? 真实链接（悬停查看）与声称的网站一致吗？
- 🧾 Request for passwords, codes or payment changes? 索要口令、验证码或变更付款？
- 📎 Unexpected attachment, especially needing macros? 意外的附件，尤其需要启用宏？
- 🔐 **Never share one-time codes** with anyone who contacts you. **绝不要把一次性验证码**告诉主动联系你的任何人。

### 4.2 Controls that work | 有效的防护措施

| Control · 措施 | Why it helps · 作用 |
| :-- | :-- |
| **Phishing-resistant MFA** (passkeys, FIDO2 security keys) · 抗钓鱼的多因素认证 | A stolen password alone is not enough, and the login is bound to the real site. 仅有被盗口令还不够，登录与真实网站绑定。 |
| **Verify out of band** · 带外验证 | Confirm requests via a known phone number or separate channel. 通过已知电话或另一渠道确认请求。 |
| **Payment procedures** · 付款流程 | Require a second approver and call-back for bank-detail changes. 变更收款信息需要第二审批人并回拨确认。 |
| **Email filtering and DMARC** · 邮件过滤与 DMARC | Block much of the bulk and spoofed mail. 拦截大量批量和伪造邮件。 |
| **Easy reporting** · 便捷上报 | A one-click "report phishing" button and a no-blame culture. 一键"报告钓鱼"和不追责的文化。 |
| **Least privilege** · 最小权限 | Limits damage when someone is fooled. 有人上当时限制损失。 |
| **Training and simulations** · 培训与演练 | Ethical, **authorized** simulations with clear consent and follow-up learning. 合乎伦理、**经授权**的演练，有明确同意和后续学习。 |

### 4.3 If you clicked or replied | 如果你已经点击或回复了

1. Do not panic; **tell your IT/security team immediately**. 别慌，**立即告知** IT 或安全团队。
2. Change the affected password from a clean device and enable MFA. 在干净的设备上修改相关口令并启用 MFA。
3. Contact your bank at once if money or card details were involved. 涉及资金或银行卡信息时，立即联系银行。
4. Report it (see references). 向有关机构举报（见参考）。

## 5. Lab / Practice | 实操（合法环境）

- 🎯 Take a **phishing quiz** and note which signs you missed. 做一份**钓鱼识别测验**，记下你漏掉的特征。
- 🔍 Practise reading **email headers** of messages you received (SPF/DKIM/DMARC results, real sender, routing). 练习阅读你收到的邮件的**邮件头**（SPF/DKIM/DMARC 结果、真实发件人、路由）。
- 🧪 Inspect suspicious links **without clicking** (hover, or use a trusted URL-scanning service; do not upload sensitive material). **不点击**检查可疑链接（悬停查看，或使用可信的 URL 扫描服务；不要上传敏感内容）。
- 🏫 If you run a simulation, do it **only with written authorization and the consent of participants**, on test accounts in your own lab. 如果要做演练，**必须有书面授权并取得参与者同意**，并在自己实验环境的测试账号上进行。

**My progress / 我的进度**
- [ ] Complete a phishing quiz and review my mistakes / 完成钓鱼测验并复盘错误
- [ ] Read the full headers of three real emails / 阅读三封真实邮件的完整邮件头
- [ ] Switch my important accounts to passkeys or security keys / 把重要账号改用通行密钥或安全密钥
- [ ] Write a one-page "what to do if I get phished" guide / 写一页"被钓鱼后怎么办"指南

## 6. References | 参考

- Google Phishing Quiz: <https://phishingquiz.withgoogle.com/>
- FIDO Alliance (passkeys): <https://fidoalliance.org/>
- APWG (Anti-Phishing Working Group): <https://apwg.org/>
- 🇦🇺 Scamwatch: <https://www.scamwatch.gov.au/> · ReportCyber (ACSC): <https://www.cyber.gov.au/report-and-recover/report>
- 🇲🇾 MyCERT: <https://www.mycert.org.my/>

---

⬅️ [Back to index | 返回目录](../README.md)
