在当今日益增长的互联网数据流中，信息安全成为了一个越来越重要的主题。数据加密不仅是保护信息免遭未授权访问的有效措施，更是隐私保护和网络安全的基石。正是在这样的背景下，高级加密标准（AES）凭借其坚如磐石的安全性和便捷的操作性，成为了全球加密技术的领航者。

在编写Web应用程序或任何需要保护信息安全的软件系统时，开发人员经常需要实现对用户信息或敏感数据的加密与解密。而AES加密算法常被选为这一任务的首选方案。在JavaScript领域，众多不同的库都提供了实现AES算法的接口，而`crypto-js`是其中最流行和最可靠的一个。接下来，就带大家深入探讨一下如何通过`crypto-js`来实现AES算法的加密与解密操作。

### AES算法简介

首先，对AES算法有一个简要的了解是必须的。AES是一种对称加密算法，由美国国家标准与技术研究院（NIST）在2001年正式采纳。它是一种块加密标准，能够有效地加密和解密数据。对称加密意味着加密和解密使用相同的密钥，这就要求密钥的安全妥善保管。

AES加密算法允许使用多种长度的密钥—128位、192位、和256位。而在实际应用中，密钥的长度需要根据被保护数据的敏感度和所需的安全级别来选择。

### 密钥长度与安全性

随着计算机处理能力的增强，选择一个充分长度和复杂性的密钥变得尤为重要。在基于`crypto-js`库编写的加密实例`encryptAES`和解密实例`decryptAES`中，密钥`encryptionKey`须保持在8、16、32位字符数，对应于AES所支持的128、192、256位密钥长度。选择一个强大的、不容易被猜测的密钥，是确保加密强度的关键步骤之一。

### 加密模式与填充

在AES算法中，所涉及的数据通过预定的方式被组织成块进行加密和解密。因此，加密模式（Encryption Mode）和填充（Padding）在此过程中扮演着重要的角色。

加密模式定义了如何重复应用密钥进行数据块的加密。`crypto-js`中的电码本模式（ECB）是最简单的加密模式，每个块独立加密，使得它易于实现且无需复杂的初始化。

填充则是指在加密之前对最后一个数据块进行填充以至于它有足够的大小。在`crypto-js`中，PKCS#7是一个常用的填充标准，它会在加密前将任何短于块大小的数据进行填充，填充的字节内容是缺少多少位就补充多少字节的相同数值。这种方式确保了加密的数据块始终保持恰当的尺寸。

### 加解密相关依赖库
加解密需要依赖有crypto-js和base-64
```js
import * as CryptoJS from 'crypto-js';
import base64 from 'base-64';
const { enc, mode, AES, pad } = CryptoJS;
var aseKey = 'youwillgotowork!';
```

### JavaScript加密实例`encryptAES`

在本文中展示的`encryptAES`函数，使用`crypto-js`库通过AES算法实现了对传入消息的加密。加密流程是，首先使用AES进行加密，然后将加密结果进行Base64编码以方便存储和传输。最后，加密后的数据可安全地被传送到需要的目的地。

```javascript
const encryptAES = message => {
  var encryptedMessage = AES.encrypt(message, enc.Utf8.parse(encryptionKey), {
    mode: mode.ECB,
    padding: pad.Pkcs7,
  }).toString();
  encryptedMessage = base64.encode(encryptedMessage);
  return encryptedMessage;
};
```

此函数接受一个参数`message`，代表需要加密的原始信息。消息首先被转换为UTF-8编码的格式，以适应AES算法的输入要求。随后，在指定ECB模式和PKCS7填充的条件下，将消息与加密密钥一同送入加密函数。在此步骤，AES算法将消息转换为一串密文，随后通过Base64编码转换为字符串形式，使得加密结果可用于网络传输或存储。

### JavaScript解密实例`decryptAES`

与加密过程相对应，解密为的是将加密后的密文还原为可读的原始信息。在`decryptAES`函数中，首先要对传入的Base64编码的加密消息进行解码，以恢复出AES算法可以直接处理的密文。然后，通过与加密过程相同的密钥和相应的ECB模式以及PKCS7填充标准进行解密，最后输出UTF-8编码的原始信息。

```javascript
const decryptAES = message => {
  var decryptedMessage = base64.decode(message);
  decryptedMessage = AES.decrypt(decryptedMessage, enc.Utf8.parse(encryptionKey), {
    mode: mode.ECB,
    padding: pad.Pkcs7,
  }).toString(enc.Utf8);
  return decryptedMessage;
};
```

在此函数中，`message`参数应是经过加密和Base64编码的字符串。解密时，加密的数据首先被Base64解码，变回AES可以直接处理的密文格式。接下来，与加密时使用同样的算法设置与密钥，通过`AES.decrypt`解密密文，然后将解密结果由于是二进制格式，通过调用`toString(enc.Utf8)`转换为UTF-8编码的可读文本。

### 效果展示
加解密的效果如下图所示：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/08729caacff742e9aee3ded7fa3e7312~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=747&h=246&s=16335&e=png&b=ffffff)