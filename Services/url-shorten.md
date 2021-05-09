# 短链接服务
[开源地址](https://github.com/zj0395/urlshortener)

## 接口
### 生成短链接
#### Request
```
curl --location --request GET 'https://url.zj0395.com/surl/create?url=http://baidu.com'
```

#### Response
```
{
    "errno": 0,
    "msg": "succ",
    "logid": "c2btk40p6f8ac67b4bj0",
    "data": {
        "code": "tBt9KO"
    }
}
```

### 解析短链接
#### Request
直接访问`https://url.zj0395.com/{$code}`，`${code}`为生成接口返回的`code`字段
