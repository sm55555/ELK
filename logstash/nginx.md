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

### Access log

```
# Logstash configuration
# Beats -> Logstash -> Elasticsearch pipeline.

input {
    pipeline {
        address => nginxa
    }
}

filter {
      grok {
        match => { "message" => ["%{IPORHOST:ip} - %{DATA:user_name} \[%{HTTPDATE:time}\] \"%{WORD:http_method} %{DATA:url} HTTP/%{NUMBER:http_version}\" %{NUMBER:response_code} %{NUMBER:body_sent_bytes} \"%{DATA:referrer}\" \"%{DATA:agent}\""] }
        remove_field => "message"
      }
      date {
        match => ["time", "dd/MMM/YYYY:HH:mm:ss Z"]
        target => "@timestamp"
        remove_field => "time"
      }
      useragent {
        source => "agent"
        target => "user_agent"
        remove_field => "agent"
      }

}

output {
        pipeline {
                send_to => output
        }
}

```


### Error log

```
# Logstash configuration
# Beats -> Logstash -> Elasticsearch pipeline.

input {
    pipeline {
        address => nginxe
    }
}

filter {
      grok {
        match => { "message" => ["%{GREEDYDATA:time} \[%{DATA:log_level}\] %{NUMBER:pid}#%{NUMBER:tid}: (\*%{NUMBER:connection_id} )?%{GREEDYDATA:messageTmp}"] }
        remove_field => "message"
      }
      date {
        match => ["time", "YYYY/MM/dd HH:mm:ss"]
	timezone => "Asia/Seoul"
        target => "@timestamp"
      }

      mutate {
        rename => {"messageTmp" => "message"}
      }

}

output {
        pipeline {
                send_to => output
        }
}

```


