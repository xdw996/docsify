## 主要方法和接口

> 获取CA签章图片

```php 
    public static String getSealImage(String password) throws Exception {
        
        //获取类型
        boolean isCa = !CAUtil.HB_CA.equals(ResourceFileUtil.getPropertyValue("caType")) ? true : false;
        
        //初始化service
        ICAService caService = initCAService(isCa, password);

        /* 获取CA印章 */
        String base64SealImg = null;
        try {
            base64SealImg = caService.getSealImageBase64();
        } catch (Exception e) {
            e.printStackTrace();
            throw new ClientException("获取签章图片失败");
        } finally {
            if (isCa) {
                caService.releaseSession();
            }
        }

        byte[] data = null;
        try (OutputStream out = new FileOutputStream(SELA_IMAGE_PATH);
             InputStream in = new FileInputStream(SELA_IMAGE_PATH);
             OutputStream out2 = new FileOutputStream(SELA_IMAGE_PATH)) {
            out.write(Base64.getDecoder().decode(base64SealImg));

            /* 将印章转为128*128 */
            Image srcImg = ImageIO.read(new File(SELA_IMAGE_PATH)).getScaledInstance(128, 128, Image.SCALE_DEFAULT);
            BufferedImage tagImg = new BufferedImage(128, 128, Transparency.TRANSLUCENT);
            Graphics graphics = tagImg.getGraphics();
            graphics.drawImage(srcImg, 0, 0, null);
            graphics.dispose();
            ImageIO.write(tagImg, "png", out2);

            /* 获取印章base64编码 */
            data = new byte[in.available()];
            in.read(data);
            base64SealImg = new String(Base64.getEncoder().encode(data), "8859_1");
        } catch (Exception e) {
            e.printStackTrace();
        }
        return base64SealImg;
    }
```