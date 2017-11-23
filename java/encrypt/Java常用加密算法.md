# 加密算法分类 #

### MD5 ###
任意长度的数据，算出的MD5值长度都是固定的。常用于文件校验。
``` Java
MessageDigest md5=MessageDigest.getInstance("MD5");
md5.update(data.getBytes());
byte[] bytes=md5.digest();
str=bytesToHexString(bytes);
```

### SHA ###
SHA比MD5更安全。

``` Java
// 获得SHA摘要算法的 MessageDigest 对象
MessageDigest sha=MessageDigest.getInstance("SHA");
// 使用指定的字节更新摘要
sha.update(data.getBytes());
//获得密文
byte[] bytes=sha.digest();
//字节数组转化为16进制字符串，方法内容在最后
str=bytesToHexString(bytes);
```

### SHA-1 ###
SHA-1以不可逆的方式将名文转换为长度固定的散列值。SHA1与MD5均由MD4导出，SHA-1摘要比MD5长32位；SHA-1运行速度较MD5慢。

### HMAC ###
Hash Message Authentication Code，散列消息鉴别码，基于秘钥的hash算法认证协议。用公开函数和秘钥产生一个固定长度的值作为认证标识。

``` Java
//生成key
SecretKeySpec keySpec = new SecretKeySpec(secretKey.getEncoded(), "HmacSHA1");
mac.init(keySpec);
//加密
byte[] rawHmac = mac.doFinal(data);
String result = new String(Base64.getEncoder().encode(rawHmac));
```
