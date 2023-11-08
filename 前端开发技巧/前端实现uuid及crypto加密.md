本文介绍了前端实现**生成uuid**和使用`crypto-js`对密码进行加密的做法，需要的小伙伴可以自取哈~
# 1. 手动实现uuid
- 1. 生成带有前缀的UUID字符串
- 2. 存储UUID的每个字符的数组
- 3. 用于生成十六进制字符的字母表
- 4. 循环36次，生成UUID的每个字符
- 5. 随机选择一个索引作为当前字符的值
- 6. 将选定的字符添加到数组中
- 7. 设置UUID的特定位置的字符
- 8. 在特定位置添加连字符
- 9. 将字符数组连接成最终的UUID字符串
- 10. 返回带有前缀的UUID字符串
```ts
// 生成带有前缀的UUID字符串
export const generateUuid = (): string => {
  // 存储UUID的每个字符的数组
  const s: string[] = [];
  
  // 用于生成十六进制字符的字母表
  const hexDigits: string = `0123456789abcdef`;
  
  // 循环36次，生成UUID的每个字符
  for (let i: number = 0; i < 36; ++i) {
    // 随机选择一个索引作为当前字符的值
    const start: number = Math.floor(Math.random() * 0x10);
    
    // 将选定的字符添加到数组中
    s[i] = hexDigits.slice(start, start + 1);
  }
  
  // 设置UUID的特定位置的字符
  s[14] = `4`;
  
  const start = (Number(s[19]) & 0x3) | 0x8;
  s[19] = hexDigits.slice(start, start + 1);
  
  // 在特定位置添加连字符
  s[8] = s[13] = s[18] = s[23] = `-`;
  
  // 将字符数组连接成最终的UUID字符串
  const uuid: string = s.join(``);
  
  // 返回带有前缀的UUID字符串
  return `supcon-${uuid}`;
};
```

# 2. 使用crypto-js加密
- 生成密钥
- 初始化向量
- 将加密内容转成字节数组
- 使用CBC模式加密
- 使用Pkcs7方式填充
- 返回加密之后的字符串
```ts
import CryptoJS from "crypto-js"; // @4.1.2

/**
 * 对内容进行加密处理
 * @param {any} content - 要加密的内容
 * @returns {string} - 加密后的字符串
 */
function encryptContent(content:any): string {
  let encryptedContent = content ?? ''; // 如果 content 为 null 或 undefined，则将其置为空字符串

  const secretKey = CryptoJS.enc.Utf8.parse("6e3d84fa5adb"); // 密钥
  const initializationVector = CryptoJS.enc.Utf8.parse("742cd20a079cd"); // 初始化向量
  const byteArray = CryptoJS.enc.Utf8.parse(encryptedContent); // 将要加密的内容转换为字节数组
  const encrypted = CryptoJS.AES.encrypt(byteArray, secretKey, {
    iv: initializationVector, // 设置初始化向量
    mode: CryptoJS.mode.CBC, // 使用 CBC 模式进行加密
    padding: CryptoJS.pad.Pkcs7, // 使用 Pkcs7 填充方式
  });

  return encrypted.toString(); // 返回加密后的字符串
}
```