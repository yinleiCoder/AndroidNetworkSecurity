# Android网络安全：加解密:secret:
RSA、DH、AES

### 需求点：

- 利用加密技术，对内容进行加密
- 对称加密
- 非对称加密
- 破解难度最高的DH密码交换方式加密技术

### 加解密技术：

> 加密技术是最常用的安全保密手段，利用技术手段把重要的数据变为乱码(加密)传送，到达目的地后再用相同or不同的手段进行还原(解密)
>
> 注：加密领域中，性能、安全，一直都是相对存在的。一个优秀的加密方案必然要能够平衡两者。

加解密的关键：安全保密、明文转换为密文、在不可信的通道上传输密文、采用相同or不同的手段进行还原为明文

加解密技术分类：

- 哈希函数——SHA256、MD5
- 对称加密——AES、DES
- 非对称加密——RSA、ECC、Elgamal
- 密钥交换——DH、ECDH

### 对称加密与非对称加密：

1. 对称加密算法——AES(Advanced Encryption Standard，高级加密标准)

- 加密：C = E(K,  P),其中P为明文，K为密钥，C为密文
- 解密： P = D(K, C),其中C为密文，K为密钥，P为明文

此算法用同一个密钥进行加解密，用同样的方式还原明文。这样的加密效率高、计算量小、使用方便，但是也会导致双方需要协商密钥、一旦密钥泄漏则加密系统崩塌、密钥更新和维护困难。

   2.非对称加密算法——RSA

RSA是一种公开密钥密码体制。

其可靠性基础：

- 大质数相乘计算很容易
- 对极大整数做因数分解很困难

例如：

- 1111 x 3331 = 3700741
- 3700741 = ? x ?

RSA加密解密是用不同的密钥，采用不同样的方式还原明文。所以公钥可以开放、密钥方便管理、安全性高，但是其性能差、加密时间长，使用起来也相对复杂一点。



### 移动端加解密：密钥交换算法——DH

DH（Diffie-Hellman），一种确保共享Key安全的穿越不安全网络的方法。

- 不需要保证信道安全
- 计算公式可完全公开:PubKey公钥可公开、PriKey私钥不公开、Secret密钥加密用
- 双方不需要知道对方的私钥
- 可协商出一个其他人都不知道的密钥



### CS网络模型:

网络模型设计——C/S3次握手

​              ----ClientHello发送客户端DH公钥--------》

客户端：<-------ServerHello发送服务器DH公钥 ----                服务器

​               ----确认收到，完成握手--------》

### AndroidStudio运行java程序，报错说找不到main函数：

在.idea下的gradle.xml文件下，<GradleProjectSettings>节点下加入<option name="delegatedBuild" value="false" />，重新build项目即可

### 抓包工具：

开发过程中调试自己的数据是否正确、服务端返回的数据是否正确会用到抓包工具，可以自由的窥探数据。

为了走代理，需要在模拟器和真机上配置一下。

设置-》WIFI-》AndroidWifi点开-》点击右上角的笔,编辑proxy。hostname填写为宿主机的ip，port填写为fiddle等抓包工具的代理端口。

### OpenSSL:开放加密库

- 完全开源的软件包，致力于解决各种信息安全
- 内部集成了非常完整的加密算法，包括RSA/AES
- 跨平台
- 功能完善，代码库相对较大

openssl安装：http://slproweb.com/products/Win32OpenSSL.html

并将bin文件夹配置到PATH里即可。

OpenSSL的windows安装包放到了项目的doc文件夹下。

生成RSA私钥：

```
genrsa -out rsa_private_key.pem 1024
```

生成RSA公钥：

```
rsa -in rsa_private_key.pem -pubout -out rsa_public_key.pem
```

如果遇到对齐失败，可以看到java中new PKCS8EncodedKeySpec是以pkcs8,而openssl默认是pkcs1，所以将上面生成的私钥转换为pkcs8：

```
 pkcs8 -topk8 -inform PEM -in rsa_private.pem -outform pem -nocrypt -out rsa_private_key.pem
```

然后还需要重新生成一下公钥：

```
rsa -in rsa_private_key.pem -pubout -out rsa_public_key.pem
```



### Java中的RSA加密库：

- 通过KeyFactory获取RSA算法实例
- 生成RSA公钥对象
- 获取RSA Cipher
- 执行加密

### Java中的AES加密库：

- 根据AES密钥创建Secret密钥向量
- 生成初始化参数向量
- 获取AES Cipher
- 执行加密
- Base64编码

### DH密钥交换：

- DH密钥交换主要是2个公式的编写：公钥计算公式、密钥计算公式
- 生成随机数作为私钥
- 预置质数原根对

------

### 将加密应用到网络通信中：

现程序中有如下模块：

- Anddroid AES加密
- Android RSA加密
- DH密钥交换
- 网络通信接口

之前抓包可以看到明文，很容易被篡改等。

所以，现串联以上的模块，流程图见doc下的截图。