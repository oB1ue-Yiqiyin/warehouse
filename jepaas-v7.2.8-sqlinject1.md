# JEPaaS v7.2.8 /je/postil/postil/loadPostil interface SQL injection vulnerability

JEPaaS /je/postil/postil/loadPostil 接口 SQL 注入漏洞

>Source code repository：https://gitee.com/ketr/jepaas-release
>
>Official website：https://jepaas.com/download
>
>Affected version: v7.2.8
>
>Official demo website：https://example.jepaas.com/login.html


## 1、分析（analysis）

The `/je/postil/postil/loadPostil` endpoint invokes the `postilService.loadPostils` method.

/je/postil/postil/loadPostil接口调用postilService.loadPostils方法

com/je/postil/controller/PostilController
<img width="2599" height="704" alt="image" src="https://github.com/user-attachments/assets/181023ef-9da6-40fa-af11-eba932cbae98" />

The `keyWord` parameter uses string concatenation, leading to a SQL injection vulnerability.

keyWord参数存在字符串拼接,导致存在SQL注入

com/je/postil/service/PostilServiceImpl
<img width="3739" height="1836" alt="image" src="https://github.com/user-attachments/assets/2b5b1741-06a4-41a9-8ade-5dcd91bc552d" />


## 2、复现（replicate）

sqlmap:

<img width="1610" height="1064" alt="Pasted image 20251205164415" src="https://github.com/user-attachments/assets/cc3751e1-7be3-4783-bcdd-803c72d1547a" />


``` http
POST /je/postil/postil/loadPostil?keyword= HTTP/1.1
Host: example.jepaas.com
SYS_SECURITY: 1
X-Requested-With: XMLHttpRequest
cachekey: 
authorization: TIUxAb8r8IilqFzx9Xg
User-Agent: Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36 Edg/109.0.1518.140
Platform-Agent: AppleWebKit/537.36 (KHTML, like Gecko)
Accept: */*
Origin: http://example.jepaas.com
Referer: http://example.jepaas.com/index.html
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Cookie: je-local-lang=zh_CN; JSESSIONID=BDFF4DEC4216AE260303D79CBE35CA06; phone=admin; password=TVRJeg==; authorization=TIUxAb8r8IilqFzx9Xg
x-forwarded-for: 127.0.0.1
Connection: close
Content-Type: application/json; charset=utf-8
Content-Length: 23

{

}
```
<br>
Attempted manual injection: when `1=1`, executed `sleep(0.3)`, and the request was delayed by 17 seconds.
<br>
尝试手动注入,1=1 时 sleep(0.3),请求延时17秒
<img width="1490" height="825" alt="Pasted image 20251205164520" src="https://github.com/user-attachments/assets/289738ab-93d4-4f08-b366-4ec0fce04108" />
<br>
When `1=1`, executed `sleep(0.4)`, and the request was delayed by 22 seconds.
<br>
1=1 时 sleep(0.4),请求延时22秒

<img width="1486" height="823" alt="Pasted image 20251205164536" src="https://github.com/user-attachments/assets/6b1d2ca2-1c51-4e1f-b79a-53092cb48bb1" />
<br>
1=2 时 sleep(0.4),无延时

When `1=2`, executed `sleep(0.4)`, with no delay.
<br>
<img width="1490" height="825" alt="Pasted image 20251205164545" src="https://github.com/user-attachments/assets/094ea13d-3596-427d-aa34-b913b3720f4f" />
<br>
可以判断数据库长度为6

It can be determined that the database name length is 6.
<br>
<img width="1315" height="978" alt="Pasted image 20251205164605" src="https://github.com/user-attachments/assets/6cd5e155-b4c3-4481-a2dd-7471a51a16b3" />
<br>
<br>

## 3、修复方案（remediation plan）
建议不要在 SQL 语句中使用字符串拼接
It is recommended not to use string concatenation in SQL.
