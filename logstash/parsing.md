### 특정 array 안에 있는 변수 꺼내서 timestamp 동기화

aws s3 logging 해주는 서비스는 대부분 json.gz 형식이고 아래와 같은 타임을 가진다.

Record안에 

ex ) 
```
"Records" => {
            "eventTime" => "2023-06=30T01:49:13Z",
            "accountId" => "12312312312323",
            ...
            }


```
 이렇게 있다.

문제점은 실제 로그가 발생해서 S3에 꽂히는 시간이랑, Kibana에 보이는 @TimeStamp가 다르게 보인다.

그러기 위해 아래와 같이 filter 설정을 해주면된다.

#### 사전에 아래 S3 Bucket 주소에 GetObject, ListBucket 권한이 필요하다 !!! 

```
input{
            s3{
                        bucket => "aws-temp-s3"
                        region => "ap-northeast-2"
                        prefix => "AWSLogs/23213/axdzxc/us-east-1/"
                        type => "iam"
                        sincedb_path => "/var/lib/logstash/plugins/input/s3/iam"
                        codec => "json"
            }
}
```

### /var/lib/logstash/plugins/input/s3 하위 폴더 권한이 logstash:logstash 가 되어야한다.

```
filter

  if [type] == 'iam' {
    if !("splitted" in [tags]){
            json{
                "eventTime" => "2023-06=30T01:49:13Z",        
            }
            split {
                        filed => "Records"
                        add_tag => ["splitted"]
            }
            date {
                        match => [ "[Records][eventTime]", "yyyy-MM-dd'T'HH:mm:ssZ"]
                        timezone => "Asia/Seoul"
            }
            mutate {
                        rename => {
                                    "type" "[@metadate][type]"
                        }
            }

    }
  }

```

아래와 같은 TimeFormat이라면 

```
2020-01-29T21:40:00.000+0000
```

```
            date {
                        match => [ "[Records][eventTime]", "ISO8601"]
                        timezone => "Asia/Seoul"
            }
```


