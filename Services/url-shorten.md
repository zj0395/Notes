# 短链接服务
## 接口
format可为`json`,`jsonp`,`simple`,`xml`  
### 生成短链接
api: https://url.zj0395.com/api.php?action=shorturl&format=json&url=URLS  
返回格式为json，取`url.keyword`或`shorturl`字段作为结果  
直接访问shorturl即会跳转到URLS

### 解析短链接
api: https://url.zj0395.com/api.php?action=expand&format=json&shorturl=SHORTURL  
返回格式json，取`longurl`

## 示例
生成：  
https://url.zj0395.com/api.php?action=shorturl&url=https://www.baidu.com&format=json
```
{
	url: {
		keyword: "r0ja37",
		url: "https://www.baidu.com",
		title: "百度一下，你就知道",
		date: "2020-02-03 05:56:40",
		ip: "125.44.94.11"
	},
	message: "https://www.baidu.com already exists in database",
	title: "百度一下，你就知道",
	shorturl: "https://url.zj0395.com/r0ja37",
	statusCode: 200
}
```

解析：  
https://url.zj0395.com/api.php?action=expand&format=json&shorturl=r0ja37  
```
{
	keyword: "r0ja37",
	shorturl: "https://url.zj0395.com/r0ja37",
	longurl: "https://www.baidu.com",
	title: "百度一下，你就知道",
	message: "success",
	statusCode: 200
}
```
