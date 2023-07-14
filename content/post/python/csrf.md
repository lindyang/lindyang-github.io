---
title: "csrf"
date: 2023-07-14T17:54:44+08:00
tags: [python]
---

全称: Cross-site request forgery

CSRF 攻击的方式
1. specially-crafted image tags(不受SOP 和 CORS 的影响)
	> `<img src=""/>`
2. hidden forms(不受SOP 和 CORS 的影响)
	> `<form action="..." method="post"></form>`
3. JavaScript XMLHttpRequests(`受`SOP 和 CORS 的影响)

对于 XMLHttpRequests

### 服务端的设置
```
def set_default_headers(self):
    self.set_header('Access-Control-Allow-Origin', 'http://www.ttt.com:9999')
    self.set_header('Access-Control-Allow-Credentials', "true")
    self.set_header("Access-Control-Allow-Headers", "content-type,x-requested-with,authorization")
    self.set_header('Access-Control-Allow-Methods', 'POST,GET,PUT,DELETE,OPTIONS')
    self.set_header('Cache-Control', 'no-store, no-cache')

def on_finish(self):
    self.set_status(204)
    self.finish()

```

###### 没有 Access-Control-Allow-Origin
```
XMLHttpRequest cannot load
http://localhost:8000/me.
Origin http://www.ttt.com:9999
is not allowed by Access-Control-Allow-Origin.
```
###### Access-Control-Allow-Origin 是 `*`
```
XMLHttpRequest cannot load
http://localhost:8000/me.
Cannot use wildcard in Access-Control-Allow-Origin
when credentials flag is true.
```
###### 没有设置 Access-Control-Allow-Headers
```
XMLHttpRequest cannot load
http://localhost:8000/me.
Request header field Content-Type
is not allowed by Access-Control-Allow-Headers.
```
### 客户端的代码
```
xmlhttp = new XMLHttpRequest();
xmlhttp.withCredentials = true;
xmlhttp.open("POST", url, true);
xmlhttp.setRequestHeader('content-type', 'application/json');
xmlhttp.onreadystatechange = function (){
    if (xmlhttp.readyState === 4){
        console.log(xmlhttp.responseText);
    }
};
var data = {"malicious": "hook fish"};
xmlhttp.send(JSON.stringify(data));
```

### form 表单
text/plain	空格转换为 "+" 加号，但不对特殊字符编码。
```
<form method="post" action="http://localhost:8000/me" enctype="text/plain">
  <input type="text" name='{"who": "' value='hook fist"}'>
  <input type="submit">
</form>
```
请求体变为: `{"who": "=hook fist"}`


防御方法:
- Synchronizer token pattern
> It can be relaxed by using per session CSRF token instead of per request CSRF token
- Cookie-to-header token
- Double Submit Cookie
- Client-side safeguards
