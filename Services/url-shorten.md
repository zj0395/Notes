# 短链接服务
## 接口
format可为`json`,`jsonp`,`simple`,`xml`  
### 生成短链接
api: http://url.zj0395.com/yourls-api.php?action=shorturl&format=json&url=YOURURL  
返回格式为json，取`url.keyword`或`shorturl`字段作为结果  
直接访问shorturl即会跳转到YOURURL

### 解析短链接
api: http://url.zj0395.com/yourls-api.php?action=expand&format=json&shorturl=SHORTURL  
返回格式json，取`result.longurl`

## 示例
生成：  
http://url.zj0395.com/yourls-api.php?action=shorturl&format=json&url=www.baidu.com  
```
{
	url: {
		keyword: "r0ja37",
		url: "http://www.baidu.com",
		title: "百度一下，你就知道",
		date: "2020-02-03 05:56:40",
		ip: "125.44.94.11",
		clicks: "4"
	},
	message: "http://www.baidu.com already exists in database",
	title: "百度一下，你就知道",
	shorturl: "https://url.zj0395.com/r0ja37",
	statusCode: 200
}
```

解析：  
http://url.zj0395.com/yourls-api.php?action=expand&format=json&shorturl=r0ja37  
```
{
	keyword: "r0ja37",
	shorturl: "https://url.zj0395.com/r0ja37",
	longurl: "http://www.baidu.com",
	title: "百度一下，你就知道",
	message: "success",
	statusCode: 200
}
```
