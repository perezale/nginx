
all I did to build it on a clean Debian was:

add contrib and non-free sources to /etc/apt/sources.list:

```
deb http://deb.debian.org/debian bullseye main contrib non-free
deb http://security.debian.org/debian-security bullseye-security main contrib non-free
deb http://deb.debian.org/debian bullseye-updates main contrib non-free
deb http://deb.debian.org/debian bullseye-backports main contrib non-free
```

```
apt update
```

install dependencies and build tools

```
apt install libpcre++-dev libpcre2-dev libpcre3-dev libssl-dev zlib1g-dev build-essential
```

Download latest pkg:/github/nginx/nginx (and patch src/http/modules/ngx_http_limit_req_module.c to add hourly and daily limits in line 915)

Lines to be added:

```c
     } else if (ngx_strncmp(p, "r/h", 3) == 0) {
                scale = 3600;
                len -= 3;
            
     } else if (ngx_strncmp(p, "r/d", 3) == 0) {
                scale = 86400;
                len -= 3;
```
     
Last, build and install with:

```
auto/configure --with-http_auth_request_module --with-http_v2_module --with-http_ssl_module --conf-path=/etc/nginx/nginx.conf --pid-path=/var/run/nginx.pid --error-log-path=/var/log/nginx/error.log --sbin-path=/usr/sbin/nginx
```

```
make install
```

this was my patch to add hours. we just need to add weeks now

```
} else if (ngx_strncmp(p, “r/w”, 3) == 0) {
                scale = 604800;
                len -= 3;
```
