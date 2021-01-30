---  
layout: post  
title: "AWS 서버에 웹페이지 올리기"  
subtitle: "AWS 서버에 웹페이지 올리기"  
categories: server
tags: AWS Server WebPage
comments: false  
---  

## AWS 서버에 WebPage 올리기

### &#128204; WebPage 제작

![aws web](/assets/aws/web.JPG)

웹페이지를 제작합니다. 저는 음원 사이트를 제작해봤습니다!&#128521;  
[https://github.com/Gayeoon/MusicWeb.git](https://github.com/Gayeoon/MusicWeb.git)  
<br>
### &#128204; 코드 옮기기

WinSCP의 `var/www/html` 폴더로 구현한 웹 페이지 코드를 옮겨줍니다.  

```sudo chmod 777-R.```

이때 관리자 권한이 필요하기 때문에 Putty를 이용해 권한을 부여해줍니다.  

![aws web](/assets/aws/web1.JPG)

코드를 옮겨줬으면 **도메인 + 폴더 경로**를 입력해서 웹 페이지로 접속해봅니다.  

<br>
### 결과화면

![aws web](/assets/aws/web2.JPG)
<br>
![aws web](/assets/aws/web3.JPG)

[https://gayeonland.site/GayeonMusic/main.html](https://gayeonland.site/GayeonMusic/main.html)  
제가 제작한 사이트 링크로 들어가보면 WebPage를 확인하실 수 있습니다!!&#128582;  
