# 06 · Cryptography Basics | 密码学基础

> 🏷️ `cryptography` `hashing` `encryption` `tls` `beginner`  
> 📅 Written: 2026-11-24 · Status: first draft / 初稿

> ⚠️ For legal, authorized learning only. 仅用于合法授权的学习。

---

## 1. Concept | 概念

Cryptography is the toolbox that provides four core guarantees:

密码学提供四项核心保证：

| Goal · 目标 | Meaning · 含义 | Typical tool · 常用手段 |
| :-- | :-- | :-- |
| **Confidentiality** · 机密性 | Only the right people can read it. 只有授权的人能读取。 | Encryption · 加密 |
| **Integrity** · 完整性 | Data was not changed. 数据没有被篡改。 | Hashes, MACs · 哈希、消息认证码 |
| **Authentication** · 认证 | You are talking to who you think. 对方确实是你以为的那个人。 | Signatures, certificates · 签名、证书 |
| **Non-repudiation** · 不可否认性 | A signer cannot deny signing. 签名者无法抵赖。 | Digital signatures · 数字签名 |

> 🧭 The golden rule: **never invent your own cryptography.** Use well-reviewed algorithms through well-maintained libraries.  
> 🧭 黄金法则：**绝不自创密码算法。** 通过维护良好的库使用经过充分审查的算法。

## 2. How it works | 原理

### 2.1 Encoding vs hashing vs encryption | 编码、哈希、加密

These three are often confused:

这三者经常被混淆：

| | Reversible? · 可逆？ | Needs a key? · 需要密钥？ | Purpose · 用途 |
| :-- | :-: | :-: | :-- |
| **Encoding** (e.g. Base64) · 编码 | ✅ Yes | ❌ No | Represent data in another format. **Not security.** 换一种格式表示数据，**不提供安全性**。 |
| **Hashing** (e.g. SHA-256) · 哈希 | ❌ No (one-way) | ❌ No | Fingerprint data, check integrity. 生成数据指纹，校验完整性。 |
| **Encryption** (e.g. AES) · 加密 | ✅ With the key | ✅ Yes | Keep data secret. 保守机密。 |

> ⚠️ **Base64 is not encryption.** Anyone can decode it. Base64 不是加密，任何人都能解码。

### 2.2 Hashing | 哈希

A cryptographic hash turns any input into a fixed-size fingerprint. Good hashes are **one-way**, **collision-resistant**, and show the **avalanche effect**: changing one character changes the output completely.

密码学哈希把任意输入变成固定长度的指纹。好的哈希具有**单向性**、**抗碰撞性**和**雪崩效应**：改动一个字符，输出就会完全不同。

```python
import hashlib
print(hashlib.sha256(b"hello").hexdigest())
# 2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824
```

| Algorithm · 算法 | Status · 现状 |
| :-- | :-- |
| MD5, SHA-1 | ❌ Broken for collision resistance; avoid for security. 抗碰撞性已被攻破，安全场景应避免。 |
| SHA-256, SHA-3 | ✅ Fine for integrity and signatures. 适用于完整性校验和签名。 |
| bcrypt, scrypt, **Argon2id** | ✅ Designed for **password** storage (slow on purpose). 专为**口令存储**设计（故意设计得慢）。 |

### 2.3 Symmetric encryption | 对称加密

The **same key** encrypts and decrypts. It is fast, so it protects the bulk of data. Common choices: **AES** (128/256-bit) and **ChaCha20-Poly1305**. The **mode** matters as much as the cipher: prefer **authenticated** modes such as **AES-GCM**, and avoid **ECB**, which leaks patterns.

加密和解密使用**同一个密钥**，速度快，适合保护大量数据。常见选择：**AES**（128/256 位）和 **ChaCha20-Poly1305**。**工作模式**和算法同样重要：优先使用 **AES-GCM** 这类**带认证**的模式，避免会泄露数据规律的 **ECB**。

### 2.4 Asymmetric encryption and signatures | 非对称加密与签名

A **key pair**: the **public key** can be shared, the **private key** must stay secret.

一对**密钥**：**公钥**可以公开，**私钥**必须保密。

| Use · 用途 | How · 方式 |
| :-- | :-- |
| Encrypt to someone · 加密给某人 | Encrypt with their **public** key; only their **private** key decrypts. 用对方**公钥**加密，只有对方**私钥**能解密。 |
| Digital signature · 数字签名 | Sign with your **private** key; anyone verifies with your **public** key. 用你的**私钥**签名，任何人可用你的**公钥**验证。 |
| Key exchange · 密钥交换 | (EC)DH lets two parties agree on a shared secret over an open channel. (EC)DH 让双方在公开信道上协商出共享密钥。 |

Common algorithms: **RSA** (use at least 2048-bit), **elliptic curves** such as **Ed25519** and **P-256**. Asymmetric crypto is slower, so real systems use it to **exchange a key**, then switch to symmetric encryption.

常见算法：**RSA**（至少 2048 位）、**椭圆曲线**如 **Ed25519**、**P-256**。非对称加密较慢，所以实际系统用它来**交换密钥**，之后改用对称加密。

### 2.5 TLS: how HTTPS puts it together | TLS：HTTPS 如何把它们组合起来

```text
Browser                                   Server
  | -- hello, supported ciphers ------->   |
  | <-- hello + certificate -----------    |  certificate proves identity  证书证明身份
  |   (browser checks the CA chain)        |  浏览器验证证书链
  | <== key exchange (ECDHE) ==========>   |  agree on a session key  协商会话密钥
  | <=== encrypted traffic (AES-GCM) ===>  |  fast symmetric encryption  高速对称加密
```

Prefer **TLS 1.3** (defined in RFC 8446); **TLS 1.0 and 1.1 are deprecated**.

优先使用 **TLS 1.3**（RFC 8446）；**TLS 1.0 和 1.1 已被弃用**。

## 3. Attack view | 攻击视角（仅限靶场 / lab only）

Good algorithms are rarely broken; **their use** is. Conceptually, weaknesses come from:

好的算法很少被攻破，出问题的往往是**使用方式**。从概念上，弱点来自：

| Weakness · 弱点 | Why it matters · 为什么重要 |
| :-- | :-- |
| Fast hashes for passwords (MD5, plain SHA-256) · 用快速哈希存口令 | Attackers can test enormous numbers of guesses per second offline. 攻击者离线每秒可尝试海量猜测。 |
| No salt · 不加盐 | Identical passwords share hashes; precomputed tables become useful. 相同口令得到相同哈希，预计算表就派上用场。 |
| Hard-coded keys · 硬编码密钥 | Keys leak through source code and repositories. 密钥会通过源码和仓库泄露。 |
| Reused nonce / IV · 重复使用 nonce / IV | Can break confidentiality of otherwise strong ciphers (e.g. GCM). 可能破坏本来很强的加密（如 GCM）。 |
| ECB mode · ECB 模式 | Equal plaintext blocks produce equal ciphertext blocks. 相同明文块产生相同密文块。 |
| Bad certificate validation · 证书验证不当 | Opens the door to man-in-the-middle attacks. 为中间人攻击打开大门。 |
| Weak randomness · 弱随机数 | Predictable keys and tokens. 密钥和令牌可被预测。 |

I deliberately do not include cracking or bypass procedures. The goal is to recognize these mistakes in code and design.

这里刻意不提供破解或绕过的步骤，目的是让你在代码和设计中认出这些错误。

## 4. Defense view | 防御视角

| Area · 方面 | Good practice · 良好实践 |
| :-- | :-- |
| **Passwords** · 口令 | Store with **Argon2id** (or bcrypt/scrypt), unique salt per user; never reversible encryption. 用 **Argon2id**（或 bcrypt/scrypt）存储，每个用户独立盐值，绝不用可逆加密。 |
| **Libraries** · 库 | Use maintained libraries (e.g. `cryptography`, libsodium); never your own algorithm. 使用维护良好的库（如 `cryptography`、libsodium），不自创算法。 |
| **Encryption mode** · 加密模式 | Use authenticated encryption (AES-GCM, ChaCha20-Poly1305). 使用认证加密。 |
| **Randomness** · 随机数 | Use a cryptographically secure source (`os.urandom`, `secrets`). 使用密码学安全的随机源。 |
| **Key management** · 密钥管理 | Keep keys out of code; use a vault or KMS; rotate and restrict access. 密钥不放进代码，用密钥库或 KMS，定期轮换并限制访问。 |
| **TLS** · TLS | Enable TLS 1.2+ (prefer 1.3), validate certificates, redirect HTTP to HTTPS. 启用 TLS 1.2 以上（优先 1.3），验证证书，HTTP 跳转 HTTPS。 |
| **Comparison** · 比较 | Compare secrets with constant-time functions (e.g. `hmac.compare_digest`). 用恒定时间函数比较机密值。 |

```python
# Password hashing with Argon2id (pip install argon2-cffi)
from argon2 import PasswordHasher

ph = PasswordHasher()              # sensible defaults, random salt included
stored = ph.hash("correct horse battery staple")
ph.verify(stored, "correct horse battery staple")   # raises an error if wrong
```

```python
# Authenticated encryption with AES-GCM (pip install cryptography)
import os
from cryptography.hazmat.primitives.ciphers.aead import AESGCM

key = AESGCM.generate_key(bit_length=256)
aes = AESGCM(key)
nonce = os.urandom(12)             # NEVER reuse a nonce with the same key
ct = aes.encrypt(nonce, b"secret message", b"context")
pt = aes.decrypt(nonce, ct, b"context")
```

## 5. Lab / Practice | 实操（合法环境）

Everything below runs on **your own machine** with **your own test data**.

下面的内容都在**你自己的机器**上、用**你自己的测试数据**运行。

```bash
# Hash a file and compare to a published checksum (Linux/macOS)
sha256sum myfile.iso

# Windows PowerShell equivalent
# Get-FileHash myfile.iso -Algorithm SHA256

# Generate a random value and a test key pair
openssl rand -hex 16
openssl genpkey -algorithm ed25519 -out test_key.pem
```

Also try: open any HTTPS site in your browser and **inspect its certificate** (issuer, validity dates, chain); see the **avalanche effect** by hashing `hello` and `hello!`; run the two Python snippets above and change one byte of the ciphertext to see AES-GCM **reject** it.

也可以试试：在浏览器里查看任意 HTTPS 网站的**证书**（颁发者、有效期、证书链）；分别对 `hello` 和 `hello!` 求哈希，观察**雪崩效应**；运行上面两段 Python，并改动密文中的一个字节，看 AES-GCM 如何**拒绝**它。

Practice platforms | 练习平台：**CryptoHack** and **Cryptopals** teach crypto by building and breaking toy systems, in a legal learning setting.

**My progress / 我的进度**
- [ ] Explain encoding vs hashing vs encryption / 讲清编码、哈希、加密的区别
- [ ] Hash a downloaded file and verify its checksum / 对下载文件求哈希并核对校验值
- [ ] Inspect a TLS certificate chain in the browser / 在浏览器里查看 TLS 证书链
- [ ] Finish the first CryptoHack challenges / 完成 CryptoHack 的入门题

## 6. References | 参考

- OWASP Password Storage Cheat Sheet: <https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html>
- RFC 8446, TLS 1.3: <https://www.rfc-editor.org/rfc/rfc8446>
- Python `cryptography` library: <https://cryptography.io/>
- CryptoHack: <https://cryptohack.org/>
- Cryptopals: <https://cryptopals.com/>

---

⬅️ [Back to index | 返回目录](../README.md)
