### 编译 Nginx 时报错

```
./configure: error: the HTTP rewrite module requires the PCRE library.
You can either disable the module by using --without-http_rewrite_module
option, or install the PCRE library into the system, or build the PCRE library
statically from the source with nginx by using --with-pcre=<path> option.
```
#### 解决方法：
在 CentOS 系统执行：
`yum -y install pcre-devel openssl openssl-devel gd-devel gcc gcc-c++`
并且执行命令附带参数`--without-http_rewrite_module`
例如：
`./configure --prefix=/export/server/nginx --with-http_ssl_module --without-http_rewrite_module`

### Nginx 在 make 时报错

```

src/event/ngx_event_openssl.c: 在函数‘ngx_ssl_load_certificate_key’中:
src/event/ngx_event_openssl.c:722:9: 错误：‘ENGINE_by_id’ is deprecated: Since OpenSSL 3.0 [-Werror=deprecated-declarations]
  722 |         engine = ENGINE_by_id((char *) p);
      |         ^~~~~~
In file included from src/event/ngx_event_openssl.h:22,
                 from src/core/ngx_core.h:84,
                 from src/event/ngx_event_openssl.c:9:                        
cc1：所有的警告都被当作是错误
```

#### 解决办法：

在编译末尾添加`--with-cc-opt="-Wno-error -Wno-deprecated-declarations"`。

例如：
`./configure --prefix=/export/server/nginx --with-http_ssl_module --without-http_rewrite_module --with-cc-opt="-Wno-error -Wno-deprecated-declarations"`