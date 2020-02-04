# 短链接服务
## 接口
### 生成短链接
api: http://url.zj0395.com/yourls-api.php?action=shorturl&url=YOURURL  
返回格式为xml，取`result.url.keyword`或`result.shorturl`字段作为结果

### 解析短链接
api: http://url.zj0395.com/yourls-api.php?action=expand&shorturl=SHORTURL  
返回格式为xml，取`result.longurl`

## 示例
生成：  
http://url.zj0395.com/yourls-api.php?action=shorturl&url=www.baidu.com  
```
<result>
  <url>
    <keyword>r0ja37</keyword>
    <url>http://www.baidu.com</url>
    <title>百度一下，你就知道</title>
    <date>2020-02-03 05:56:40</date>
    <ip>125.44.94.11</ip>
    <clicks>1</clicks>
  </url>
  <message>http://www.baidu.com already exists in database</message>
  <title>百度一下，你就知道</title>
  <shorturl>http://url.zj0395.com/r0ja37</shorturl>
  <statusCode>200</statusCode>
</result>
```

解析：  
http://url.zj0395.com/yourls-api.php?action=expand&shorturl=r0ja37  
```
<result>
  <keyword>r0ja37</keyword>
  <shorturl>http://url.zj0395.com/r0ja37</shorturl>
  <longurl>http://www.baidu.com</longurl>
  <title>百度一下，你就知道</title>
  <message>success</message>
  <statusCode>200</statusCode>
</result>
```
