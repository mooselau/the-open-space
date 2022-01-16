# 1 Intro
This article explains various basic knowledges for computer science.

# 2 How Computer Works
This is where explains basic knowledges about Computing in Computer, thery are essential to understand the data flow and manipulation inside computer.

## 2.1 Encoding and Character Sets
Please refer to this link for knowledge: Encoding and Character Sets.

# 3 How Web / Internet Works

## 3.1 Session and Cookie
Refer to this link for knowledge: Session and Cookie.

# 4. Internet Security
Cyber security is a very essential topic to cover in this section.

Will explain the concept of encryption & decryption, the symmetric & asymmetric concept, and a practical CSRF concept, also will cover some protection strategy, e.g. JWT token etc. .

## 4.1 Encryption/Decryption Concepts
目前信息安全领域有两种常见的加密解密方法：对称加密(Symmetric Encryption)和非对称加密(Asymmetric Encryption)。

### 4.1.1 Symmetric Encryption & Asymmetric Encryption
设想一个场景: Alice 需要和 Bob 通过email交流，涉及到机密信息的时候，会想到加密邮件内容。
对称加密可以简单的理解为：
Alice将文件通过密钥加密，然后发送给Bob；Bob为了可以揭秘邮件内容，也需要一份Alice的密钥COPY来做解密工作。
这时候问题就出现了：Alice要怎么把密钥COPY发送给Bob？
直接发送就可能被“中间人”(man-in-the-middle)盗取，从而可以解密所有Alice发送给Bob的邮件。反之亦然，Bob也需要冒着风险把密钥发送给Alice。
非对称加密就是为了解决这个问题而出现的。
首先，Alice和Bob通过主流的加密解密算法(如：RSA)各自生成了一对 private/public key。其中，public key是给其他人用来加密的密钥（公钥），private key是Owner自己用来解密的密钥（私钥）。
这种情况下，Alice只需要把自己的public key发给Bob，然后Bob通过Alice的public key进行加密，之后把邮件密文发给Alice，然后Alice再通过自己的private key进行解密看到原文。反之亦然，Alice通过Bob的public key进行加密，然后Bob通过自己的private key进行解密看到原文。
这样的话，所有外人包括“中间人”都没有办法看到原文，拿到public key也只能进行加密操作。
RSA这样的加密解密算法保证了，无法通过public key来算出private key的。
同时另一方面，private key也是非常重要的，不可以公布出去的东西。否则所有Alice接收到的Bob的邮件都可能被其他人解密。

## 4.2 CSRF
CSRF → anti_csrf → _csrf _token → stores in Cookie or Session.
a link to Spring Security:
crsf.disable();
permitall.antMatchers;
xforms.disable;

## 4.3 JWT
JWT, Cookie, Session

# 5. Algorithms
算法策略在中文百度中，可以主要分成四大类：1. 分治算法；2. 贪心算法(
greedy algorithm)；3. 动态规划算法(dynamic programming)；4. 回溯算法(back tracking method)；