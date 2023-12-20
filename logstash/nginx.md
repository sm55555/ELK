## Nginx 로그 파싱 방법

아래와 같은 log format 존재 시

```
    log_format main '$remote_addr - $remote_user [$time_local] '
        '"$request" $status $body_bytes_sent "$http_referer" '
        '"$http_user_agent" "$http_x_forwarded_for"';
    log_format static_main '$remote_addr [$time_local] '
        '"$request" $status $body_bytes_sent '
        '"$http_user_agent"';

```


