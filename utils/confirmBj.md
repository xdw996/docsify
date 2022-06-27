## 主要方法和接口

> 确认报价表单
```php 
    public Boolean checkKbAFrom(String uuId, String jsonStr){
        String message = "";
        List<Element> elementTitles=null;
        JSONObject jsonObject = JSON.parseObject(jsonStr);
        JSONArray jsonArray = JSON.parseArray(jsonObject.getString("colList"));
        if(getMaxHeadLevelNum(uuId)>0){
            elementTitles=getColsByRows(uuId,jsonArray);//获取行节点
        }else{
            elementTitles= document.selectNodes("//SFS/SF[@UUID='" + uuId + "']/COLS/COL");
        }
        Element elementRaw = (Element) document.selectSingleNode("//SFS/SF[@UUID='" + uuId + "']/ROWS/ROW");
        message = checkFields(elementTitles, jsonArray, elementRaw.attributeValue("CONTROL_PRICE"));
        if ("".equals(message)) {
            return true;
        }else{
            AlertUtil.alert(message);
            return false;
        }
    }
```