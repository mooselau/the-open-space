# 1 Intro
国密(Chinese Crypto Algorithms)在最近几年国内金融领域应用越来越广，考虑到踩过一些坑，在这里分享出来。

# 2 Concept & Formats
众所周知，常见的证书一般是一对一对出现的，其中，公钥证书是用来给外部的人进行加密的，私钥证书是用来给内部人士用进行解密的，签名阶段正好相反，私钥证书是用来进行加签操作，公钥证书是用来进行验签操作。

常见的公钥的文件格式一般可以是 .PEM, .DER等，在读取的时候一般是需要使用X509的对象格式结构进行载入。

> 当然，有些时候可能会出现 .CER 格式的公钥证书，这时候需要从公钥证书中提取出公钥，可以通过PEMReader这类接口来进行load，然后进行格式判断再进行公钥载入。

相对应的，私钥文件格式一般都是 .KEY，在读取的时候一般都是使用 PKCS12 的对象格式结构进行载入。

一般公钥和私钥统称为 公私钥对，即 Keypair。

# 3 International Crypto Algo & Chinese Crypto Algo

一般国际算法中常见的非对称加密算法如 RSA，在国密算法体系中有 SM2 与之对应；对称加密算法如 AES，在国密算法中有 SM4 与之对应；另外 摘要算法(Digest) 有SM3 与国际算法 SHA256/MD5 等对应。

# 4 Chinese Crypto Related Projects

国密算法发布后，国际项目如 OpenSSL，BouncyCastle 都开始支持，所以如果只是单纯希望支持标准的国密算法，通过使用 BouncyCastle 就可以做到（这也是非常推荐的）。

考虑到BouncyCastle的接口和使用学习曲线比较陡，可以参考这个Github 开源项目，它封装了一层 BouncyCastle 同时提供了较为丰富的业务接口来使用：[GMHelper Github](https://github.com/ZZMarquis/gmhelper)

另附上 BouncyCastle Org Java首页：[Bouncy Castle Java Page](https://www.bouncycastle.org/java.html)

# 5 GM & CFCA

除了国际项目，在国内，尤其是金融领域，很多时候会逃不开 CFCA - 国内一个具有国密资质的供应方，如果使用 CFCA 颁发的国密证书，可能面临需要配套使用 CFCA 的硬件加密机或者软件工具包来做出可用的国密项目。

## 5.1 CFCA GM

CFCA GM 是有一个 Java Lib 可以使用的，他们的lib提供了单证/双证的 P10 生成的接口，可以用来生成 P10 CSR 文件。

> CSR 文件是一个包含了 一张公钥的证书请求文件，一般在申请国密的时候会需要用到，比如通过NUCC/网联平台，在提供了合法的 CSR 之后，NUCC 会帮忙通过 CFCA 申请下来一套 双证的国密证书，其中包含：一份签名公钥证书，一份加密公钥证书，一份（加密）私钥证书。

> 制作 CSR 文件，需要注意的是 DN(Domain Name)，比如通过网联申请国密证书，DN 必须符合一定的格式，如果不是这个格式，会导致 CSR 在 NUCC 直接验证失败。
> 另外，制作双证 CSR 的过程中，会需要用到 两套临时证书：一套临时证书用来加密 NUCC 申请下来的私钥文件，另一套临时证书用来生成 CSR 文件。

>单双证的区别？
>其实目前还不是非常清楚，但是可以确定的是，曾经的 RSA 国际算法，就是典型的单证（一套证书）类型：加解密和签名只需要一套证书，而对应的国密双证，就是需要两套（或者多套）证书来做不同的事情：比如 加密需要一套证书，签名需要另一套证书。

## 5.2 CFCA GM Application

![GuoMi Application via NUCC](/images/gm-algo.png)

如上图所示为国密通过 NUCC / 网联 大致的申请流程，看得出来，NUCC 其实也是通过 CFCA 来申请国密双证书的，申请完了会提供下载链接给客户下载使用，其中在生成 CSR 文件的过程中会用到两套临时证书，他们对申请下来的证书文件来说都很重要。

详细说来，其中 CSR 公私钥对中的 公钥是包含在 CSR文件中的，私钥是可以和申请下来的 签名公钥组成 公私钥对进行使用的；Temp 公私钥对中的 私钥则会用来加密申请下来的 国密私钥；而申请下来的加密公钥和私钥（解密后）会是一对 加解密的公私钥对。

# 6 Appendix A Online Tools

以下提供一些有用的工具链接：

证书查看工具：https://myssl.com/cert_decode.html

ASN1查看工具：https://lapo.it/asn1js/

GMHelper Github: https://github.com/ZZMarquis/gmhelper

## 6.1 GMSSL
[GMSSL Github](https://github.com/guanzhi/GmSSL/) 相对于 OpenSSL，是国人制作的 SSL 工具，用来生成国密相关证书的 SSL 工具，不过由于缺少足够的维护可用性存疑。
