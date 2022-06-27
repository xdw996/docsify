## 流程图
![logo](../img/img16.png ':size=850x500')

## 主要方法和接口

>根据key读取配置文件值
```php 
  public static String getPropertyValue(String key) throws ClientException {
        try (FileInputStream in = new FileInputStream(resourcePath)) {
            Properties props = new Properties();
            props.load(in);
              return props.getProperty(key);
            } catch (IOException e) {
              e.printStackTrace();
              throw new ClientException("读取配置失败");
            }
      }
```

>对24位随机字符串base64加密,生成32位随机数 (zxsCom.jar接口)
```php 
    public String generateSymKey() throws Exception {
        return xam.SOF_GenRandom(24);
    }
```

>加密8位自定义密码信封
```php 
    public String generateEncryptKey(String passwordKey) throws Exception {
        passwordKey = passwordKey + passwordKey + passwordKey;
        byte[] b=null;
        String s=null;
        try {
            b = passwordKey.getBytes("utf-8");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        if(b!=null){
            s=new BASE64Encoder().encode(b);
        }
        return s;
    }
```

>非对称加密 对称秘钥对 (zxsCom.jar接口)
```php 
    public String encryptSymmetricSecret(String passwordKey) throws Exception {
        return xam.SOF_PubKeyEncrypt(xam.SOF_ExportExChangeUserCert(certid),passwordKey);
    }
```

>非对称解密 (zxsCom.jar接口)

加密后会进行一次解密测试,测试加密是否成功
```php 
    public String decryptSymmetricSecret(String strSymKey) throws Exception {
        return xam.SOF_PriKeyDecrypt(certid,strSymKey);
    }
```

>通过对称秘钥对,对文件进行对称加密 (zxsCom.jar接口)
```php 
    public boolean encryptFile(String fileInPath, String fileOutPath, String strSymKey) throws Exception {
        File out = new File(fileOutPath);
        if(!out.exists()){
            out.createNewFile();
        }
        Boolean res = xam.SOF_SymEncryptFile(strSymKey,fileInPath,fileOutPath);
        return res;
    }
```

>获取加密文件摘要
```php 
    private static String getDigestFile(String filePath) throws Exception {
        if ("SHECA".equals(ResourceFileUtil.getPropertyValue("caType"))) {
            try (InputStream input = new FileInputStream(filePath)) {
                StringBuffer strDigest = new StringBuffer(16);
                int n = 0;
                byte[] buffer = new byte[MAX_LENGTH];
                while (-1 != (n = input.read(buffer))) {
                    if (n == MAX_LENGTH) {
                        strDigest.append(getDigest(Base64.getEncoder().encodeToString(buffer)));
                    } else {
                        byte[] bb = new byte[n];
                        System.arraycopy(buffer, 0, bb, 0, n);
                        strDigest.append(getDigest(Base64.getEncoder().encodeToString(bb)));
                    }
                }
                return strDigest.toString();
            } catch (Exception e) {
                e.printStackTrace();
                return null;
            }
        } else {
            String digestAlgorithm = ResourceFileUtil.getPropertyValue("digestAlgorithm");
            MessageDigest messagedigest;
            try (FileInputStream in = new FileInputStream(new File(filePath));) {
                if ("SHA256".equals(digestAlgorithm)) {
                    messagedigest = DigestUtils.getSha256Digest();
                } else if ("SHA1".equals(digestAlgorithm)) {
                    messagedigest = DigestUtils.getSha1Digest();
                } else {
                    messagedigest = DigestUtils.getSha1Digest();
                }
                byte[] buffer = new byte[1024 * 1024 * 10];
                int len = 0;
                while ((len = in.read(buffer)) > 0) {
                    //该对象通过使用 update（）方法处理数据
                    messagedigest.update(buffer, 0, len);
                }
                return Base64.getEncoder().encodeToString(messagedigest.digest());
            } catch (Exception e) {
                e.printStackTrace();
                throw new ClientException("获取摘要失败");
            }
        }
    }
```

>获取对数据签名结果 (zxsCom.jar接口)
* 获取压缩后的投标文件签名结果
* 获取加密文件签名结果
```php 
    public String getDataSignResult(String fileSourceData) throws Exception {
        return xam.SOF_SignData(certid,fileSourceData);
    }
```

