## 主要方法和接口

>初始化CAService
```php 
    public static ICAService initCAService(boolean isNeedCa, String caPassword) throws Exception {
        if (isNeedCa) {
            if (caPassword == null || caPassword.length() <= 0) {
                throw new ClientException("CA密码不能为空！");
            }
        }

        String caType = ResourceFileUtil.getPropertyValue("caType");
        if (CF_CA.equals(caType)) {
            return new CFCAService(isNeedCa, caPassword);
        } else if (BJ_CA.equals(caType)) {
            return new BJCAService(isNeedCa, caPassword);
        } else if (HUN_CA.equals(caType)) {
            return new HNCAService(isNeedCa, caPassword);
        } else {
            throw new ClientException("CA初始化失败，请选择CA类型！");
        }
    }
```

>加密解密方法

详情见`加密,解密`章节
```php 
    public static String encrypt(String fileInPath, String fileOutPath, String bidType, Map<String, String> dataMap, String caPassword, String mailerPassWord)

    public static void decrypt(String filePath, String decryptType, String password, String tmpPath)
```

>创建XML
```php 
    public static void createHistoryXml(String bidType, String filePath, String outPath)
    
    //核心代码
    SAXReader saxReader = new SAXReader();//添加信息
    document = saxReader.read(new File(filePath));
    root = document.getRootElement();
    Element channel = root.addElement("HISTORY_DATA");
    writer = new XMLWriter(new FileOutputStream(new File(filePath)), OutputFormat.createPrettyPrint());
    writer.setEscapeText(false);//字符是否转义,默认true
    writer.write(document);
```