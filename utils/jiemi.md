## 流程图
![logo](../img/img15.png ':size=850x500')

## 主要方法和接口

>获取签名证书公钥字符串
```php 
    public String getSignCertInfo() throws Exception {
        if(cert == null){
            cert = xam.SOF_ExportUserCert(certid);
        }
        return cert;
    }
```

>非对称解密对称秘钥对
```php 
    public String decryptSymmetricSecret(String strSymKey) throws Exception {
        return xam.SOF_PriKeyDecrypt(certid,strSymKey);
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

>验证CRC32
```php 
   public static void unZipCurrent(String inFilePath, String destDir) throws ClientException {

        FileUtil.delAllFile(destDir,false);

        File zipfile = new File(inFilePath);
        if(!zipfile.exists()){
            LogUtil.error("验证CRC32失败,"+inFilePath+"不存在");
            throw new ClientException("验证CRC32失败");
        }

        ZipFile zipFile = null;
        try {
            zipFile = new ZipFile(zipfile,"GB18030");
            Enumeration<ZipEntry> enumeration = zipFile.getEntries();
            ZipEntry zipEntry = null ;
            while (enumeration.hasMoreElements()) {
                zipEntry = (ZipEntry) enumeration.nextElement();
                File loadFile = new File(destDir + UUID.randomUUID() + "." + FilenameUtils.getExtension(zipEntry.getName()));
                if (zipEntry.isDirectory()) {
                    continue;
                } else {
                    if (!loadFile.getParentFile().exists())
                        loadFile.getParentFile().mkdirs();
                    OutputStream outputStream = new FileOutputStream(loadFile);
                    InputStream inputStream = zipFile.getInputStream(zipEntry);
                    long res = doChecksum(zipFile.getInputStream(zipEntry));
                    IOUtils.copy(inputStream, outputStream);
                    IOUtils.closeQuietly(inputStream);
                    IOUtils.closeQuietly(outputStream);
                    if(zipEntry.getCrc()!=res){
                        LogUtil.error("验证CRC32失败;zip:"+zipEntry.getCrc()+";解压后:"+res+","+zipEntry.getName());
                        throw new ClientException("验证CRC32失败");
                    }
                }
            }
            LogUtil.info("验证CRC32成功,"+inFilePath);
        } catch (Exception e) {
            e.printStackTrace();
            throw new ClientException(e.getMessage());
        }finally {
            ZipFile.closeQuietly(zipFile);
            FileUtil.delAllFile(destDir,false);
        }
    }
```
