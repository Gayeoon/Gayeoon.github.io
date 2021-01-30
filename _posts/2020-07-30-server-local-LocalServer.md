---  
layout: post  
title: "Local 서버 구축하기"  
subtitle: "Local 서버 구축하기"  
categories: server
tags: local Window Server Portforwarding
comments: false  
--- 

## APM 설치

### &#128204; Bitnami WAMP 다운

[https://bitnami.com/stack/wamp](https://bitnami.com/stack/wamp) 사이트에서 본인 컴퓨터 OS 맞는 버전을 다운받습니다.  

![apm install step](/assets/localserver/step1.JPG)

오래걸립니다..&#128164;  

![apm install step](/assets/localserver/step2.JPG)

설치가 완료되면 다음과같이 APM이 한번에 설치가 됩니다! &#128518;  

`wampstack-7.4.8-0 -> apache2 -> htdocs` 폴더에서 phpinfo 코드를 작성해줍니다.  

```
<?php
	phpinfo();
?>
```
<br>
### 결과화면

![apm install result](/assets/localserver/step4.JPG)

localhost/index.php에서 php가 잘 설치된 것을 확인할 수 있습니다.  

![apm install result](/assets/localserver/step5.JPG)

포트포워딩 전 index.html을 조금 수정해봤습니다. &#128518;
<br>
<hr>
## 포트포워딩

### &#128204; 포트포워딩 설정

cmd 창에서 `ipconfig` 명령어를 통해 ip를 확인합니다.  

![Portforwarding step](/assets/localserver/step9.png)

**A IP**를 주소창에 넣으면 공유기 설정 페이지로 갈 수 있습니다.  
고급 설정 -> 포트 포워딩에서 포트를 설정해줍니다.  

![Portforwarding step](/assets/localserver/step10.png)

내부 IP 주소에는 cmd 창을 통해 알게된 **B IP**를 넣어주시면 됩니다.  
서비스 포트와 내부 포트는 같아야합니다.  
~~저는 TCP와 UDP 모두 설정해주었습니다.~~  

A IP는 **내부 IP 주소**이기 때문에 다른 기기에서 접속하기 위해서는 **외부 IP 주소**가 필요합니다.  

![Portforwarding step](/assets/localserver/step14.png)

외부 IP 주소는 공유기 설정 페이지에 로그인 할때 나옵니다.
<br>
### 결과화면

![Portforwarding result](/assets/localserver/step13.jpg)

![Portforwarding result](/assets/localserver/step12.jpg)

LTE를 통해 접속했을때, Apache와 PHP의 index 화면으로 잘 접속됩니다!! &#128582;