# Decption Project

> [OpenResty Reference](https://openresty-reference.readthedocs.io/en/latest/Lua_Nginx_API/#ngxreqget_uri_args)

## 匹配的位置：

1. parameter_value, 方法：ngx.req.get_uri_args():value

2. parameter_string, 方法：ngx.req.get_uri_args() key + value

3. uri, 

4. request_body, 方法：ngx.req.get_body_data()

5. header: ngx.req.get_headers()

   - cookie
     - 获取所有cookie： ngx.var.http_cookie,  这里获取的是一个字符串，如果不存在则返回nil 。
     - 获取单个cookie： ngx.var.cookie_username, 获取单个cookie，_后面的cookie的name，如果不存在则返回nil 。

   - refer,

   - x-forwarded-for, 

   - user-agent:

