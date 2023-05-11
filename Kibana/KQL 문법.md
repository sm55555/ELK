## KQL 

임의의 String 값이 포함된 경우 (web-server)가 포함된

```kql
hostname : "web-server"
```


필드가 company이고 value가 naver 인 경우

```kql
company : naver
```

필드가 company이고 value가 naver and country가 korea인 경우

```kql
company : naver and country : korea
```


country가 asg를 안 포함하는 경우

```kql
not country : *asg*
```



