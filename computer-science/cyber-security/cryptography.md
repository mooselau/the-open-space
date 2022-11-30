# 1 Intro
计算机技术中的密码学

# 2 Symmetric Cryptographic Algorithm 对称加密算法
## 2.1 AES
AES 是非常广泛地使用的对称加密算法。常见的有AES256算法。

![AES](https://www.baeldung.com/wp-content/uploads/2020/11/Figures.png "AES")

### 2.1.1 AES Parameters
在AES的各种演变的算法中，都会涉及到盐度(salt)，初始向量(iv)，这两个参数解密也同样需要，同时要保证一样才可以正常解密。

> 在实际应用中，salt和iv都会组装在加密内容中，比如encrypted bytes = iv bytes + salt bytes + encrypted message bytes，然后通过base64 encode成可以展示的string，解密方在获取到密文和密钥后，可以先通过Base64 decode 然后拆解bytes，进而获得iv，salt 和 密文，之后正常解密得到明文。
所以，实际使用时，加密方需要告知解密方：
>- 密文
>- 密钥
>- salt 和 iv 的字节长度
>这样解密方才可以正常解密内容。

参考：(Java AES Encryption and Decryption - baeldung)[https://www.baeldung.com/java-aes-encryption-decryption]

## 2.2 DES

## 2.3 SM4
在国内营业的金融机构为了合规，需要支持使用国内认可的对称加密算法，其中SM4就是典型的国密对称加密算法。

# 3 Asymmetric Cryptographic Algorithm 非对称加密算法
## 3.1 RSA
RSA 是非常经典的非对称加密算法，和对称加密算法不一样的是，他有两个key(密钥)，其中公钥是用来加密数据的，私钥是用来解密数据的。

Github: https://github.com/mooselau/springboot-tiny/tree/master/gooseonroad/purejava/src/main/java/crypto

参考：[RSA in Java - baeldung](https://www.baeldung.com/java-rsa)

### 3.1.1 RSA KeyPair
公私钥在密码中常称为KeyPair，如何自主生成一对公私钥呢？

__Step 1 Generate Private Key__
先生成2048位的私钥：
```sh
$ openssl genrsa -out private_key.pem 2048 
```

__Step 2 Convert to PKCS#8 (DER Format)__
为了让Java可以读取这个Key，这里采用DER格式，进行一次转换：
```sh
$ openssl pkcs8 -topk8 -inform PEM -outform DER -in private_key.pem -out private_key.der -nocrypt
```

__Step 3 Output Public Key in DER Format__
同样，为了让Java可以读取这个Key，采用DER格式，从PEM私钥中导出来：
```sh
$ openssl rsa -in private_key.pem -pubout -outform DER -out public_key.der
```
至此，我们就有了DER格式的公私钥文件了。

## 3.2 SM2
由于金融机构在国内运营需要遵循使用国密算法，国家认可的密码算法，其中SM2 就是典型的非对称算法之一。

# 4 Digest Algorithms 摘要算法
SHA256, MD5, Hashing..
SM3 是典型的国密算法中的Digest算法。