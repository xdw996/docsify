## 主要方法和接口

>检验文件可进行操作
```php
   FileUtil.checkFileOperable(filePath, fileType);
```

>删除所有临时文件
```php
   FileUtil.delAllFile(ConstantUtil.TEMP_FILE_Path, false);
```

>解压投标文件
```php
   ZipFileUtil.unZipFile(filePath, ConstantUtil.TEMP_FILE_Path);
```

>创建当前项目根文件夹
```php
   rootFile.mkdir();
```

>创建投标资格预审文件,主信息配置文件
```php
   createIniFile(packageId, fileType, "0");
```

>打开投标文件主页面,创建完成
```php
   new MainApp(MainApp.BID_RESOURCE, new BidController(packageId, ConstantUtil.BUSINESS_TYPE, ConstantUtil.TENDEREE_TYPE), MainApp.stage);
```

