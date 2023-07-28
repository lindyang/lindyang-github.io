---
title: "Host"
date: 2023-07-28T15:57:13+08:00
tags: [nginx]
---


 |     变量     | 端口显示 | 值存在
:---------------|:-------| :---
http_host    |    ✔   | "Host: ..." ? ✔ : ✖
host            |    ✖   |"Host: ..." ? ✔ : (server_name ? ✔ : ✖)
proxy_host | 80 ? ✖ :  ✔ | proxy_pass ? ✔ : ✖
[参考](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_set_header)
```
An unchanged “Host” request header field can be passed like this:

  proxy_set_header Host       $http_host;

However, if this field is not present in a client request header then nothing will be passed.
In such a case it is better to use the $host variable -
its value equals the server name in the “Host” request header field
or the primary server name if this field is not present:

  proxy_set_header Host       $host;
```

```
server {
    listen 8000;
    server_name lindyang.com;
    default_type text/html;
    location / {
        proxy_pass http://127.0.0.2:8888;  # 8888 -> 80
        proxy_set_header Host $http_host;
        proxy_set_header X-Proxy-Host $proxy_host;
    }
}

server {
    listen 8888;  # 8888 -> 80
    server_name yld.com www.yld.com;
    default_type text/html;
    location / {
        return 200 'http_host=[$http_host] host=[$host] proxy_host=[$http_x_proxy_host]\n';
    }
}
```

- **不携带**请求头 Host
> `curl -H 'Host:'` **`--http1.0`** `http://lindyang.com:8000`
> ```
> http_host=[] host=[yld.com] proxy_host=[127.0.0.2:8888]
> ```
>  |     变量     |           值       | 说明 |
> :---------------|:-----------------|:-----
> http_host    |                     | 请求头无 Host, 则 http_host 为空
> host            |    yld.com     | 忽略空 Host , 使用 server_name 的第一项
> proxy_host |127.0.0.2:8888| 取自于 proxy_pass 的参数


- **携带**请求头 Host
> ###### 为验证 proxy_host 不带端口的情况，需将 8888 -> 80
> `curl -H 'Host: Dummy:abc' http://lindyang.com:8000`
> ```
> http_host=[dummy:abc] host=[dummy] proxy_host=[127.0.0.2]
> ```
>  |     变量     |           值       | 说明 |
> :---------------|:-----------------|:-----
> http_host    |  Dummy:abc  | 给啥拿啥
> host            |    dummy      | 第一个`:`前的内容(d小写)
> proxy_host |127.0.0.2| 省略端口 80


