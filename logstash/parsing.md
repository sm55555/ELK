### 특정 array 안에 있는 변수 꺼내서 timestamp 동기화

aws s3 logging 해주는 서비스는 대부분 json.gz 형식이고 아래와 같은 타임을 가진다.

Record안에 

ex ) 
```
"Records" => {
            "eventTime" => "2023-06=30T01:49:13Z",
            "accountId" => "12312312312323",
            .....
            }


```
 이렇게 있다.

문제점은 실제 로그가 발생해서 S3에 꽂히는 시간이랑, Kibana에 보이는 @TimeStamp가 다르게 보인다.

그러기 위해 아래와 같이 filter 설정을 해주면된다.

```
filter

  if [type] == 'iam' {
    if !("splitted" in [tags]){
        json {
            source => "message"
        }
        json {
            source => "message"
        }
    }
  }

```



