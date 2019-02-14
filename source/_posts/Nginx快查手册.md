---
title: Nginx快查手册
date: 2019-02-14 09:06:39
tags:
    - Nginx
    - 快查手册
---

>因为上手时间不长，用得少，很多配置容易忘记。所以做了快查手册，以便自己及时获取，快速解决问题。

### Nginx 常见配置

#### proxy_pass的使用 - 多服务同端口

可以解决如 同一服务器下部署多个项目 `Ajax` 请求跨域的问题
---

<font  color=#F56C6C face="微软雅黑">常用</font>
```nginx
location /proxy/ {
    proxy_pass http://127.0.0.1/; #代理到URL：http://127.0.0.1/test.html
}
```
---
```nginx
location /proxy/ {
    proxy_pass http://127.0.0.1; #代理到URL：http://127.0.0.1/proxy/test.html
}
```


```nginx
location /proxy/ {
    proxy_pass http://127.0.0.1/aaa/; #代理到URL：http://127.0.0.1/aaa/test.html
}

```


```nginx
location /proxy/ {
    proxy_pass http://127.0.0.1/aaa; #代理到URL：http://127.0.0.1/aaatest.html
}
```

#### 同端口不同二级域名

添加多个 `server` => `listen` **相同** 端口， `server_name` 字段指定二级域名 即可。
