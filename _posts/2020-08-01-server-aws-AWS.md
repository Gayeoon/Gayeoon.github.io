---  
layout: post  
title: "AWS 서버 구축하기 - 도메인 & HTTPS 적용"  
subtitle: "AWS 서버 구축하기 - 도메인 & HTTPS 적용"  
categories: server
tags: AWS Server Domain HTTPS
comments: false  
--- 

## AWS 서버 구축 - 2

### &#128204; PHPMyAdmin 설치

```
apt-get update
apt-get install phpmyadmin
```

![aws install step](/assets/aws/aws1.JPG)

불안하게 생긴 핑크화면이다.. 나는 Nginx를 설치했기때문에 apache는 설치하지 않겠다.&#128581;  
Tab 키를 이용해서 OK를 선택해줍니다.  

![aws install step](/assets/aws/aws2.JPG)

No를 선택해줍니다.  
[https://jimnong.tistory.com/808](https://jimnong.tistory.com/808) 	&#10140; 참고해주세요!!  

```
cd /var/www/html/
sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
```

nginx에서 phpMyAdmin을 사용하기 위해 conf 파일을 고치는 부분이 있었는데, 이렇게 심볼릭 링크를 걸어주면 간단합니다. ~~(yezzi 블로그 참고)~~  
바탕화면에서 바로가기 버튼같은 역할을 한다고 합니다.&#128580;~~(yezzi 블로그 참고)~~  

![aws install step](/assets/aws/aws3.JPG)

[IP주소/phpmyadmin/]에 접속하면 다음과 같은 화면을 보실 수 있습니다.  

<br>
#### &#128161; ERROR &#128161;

![aws install step](/assets/aws/aws4.JPG)

방금 MySQL 하고 왔는데 비밀번호가 틀렸다는 에러가 나왔습니다.&#128544;  
[https://to-dy.tistory.com/58](https://to-dy.tistory.com/58) &#10140; 이 블로그를 참고해서 비밀번호를 재설정 해줍니다.  

<br>
### 결과화면

![aws install step](/assets/aws/aws6.JPG)

헉 성공했습니다!! &#128582;
<br>
<hr>
### &#128204; Domain 적용하기

원하는 이름으로 도메인을 구입해줍니다.  
[https://www.gabia.com/](https://www.gabia.com/)  
[https://whois.co.kr/](https://whois.co.kr/)  


![aws install step](/assets/aws/domain1.JPG)

도메인 구입 후 AWS IP와 연결해주기 위해 AWS 콘솔에 접속합니다.  

![aws install step](/assets/aws/domain2.JPG)

호스팅 영역을 생성한 뒤, **값, 트래픽 라우팅 대상** 에 적혀있는 네개의 **네임 서버**를 복사합니다.  

![aws install step](/assets/aws/domain3.JPG)

방금 구매했던 가비아의 도메인 관리툴로 들어가줍니다.  

![aws install step](/assets/aws/domain4.JPG)

AWS에서 만들었던 **네임 서버**를 차례대로 등록시켜줍니다.  

![aws install step](/assets/aws/domain5.JPG)

AWS로 돌아와서 레코드를 생성해줍니다.  
이때 레코드 이름은 www, 값, 트래픽 라우팅 대상에는 AWS의 IP를 입력해줍니다.  

<br>
![aws install step](/assets/aws/later.jpg)
<br>

<br>
### 결과화면

![aws install step](/assets/aws/result3.JPG)

가연랜드 도메인 띄우기 성공했습니당~&#128518;
<br>
<hr>
### &#128204; nginx에 https 적용하기

Let's Encrypt는 다양한 plugin들을 통해서 SSL 인증서를 제공합니다.  
저는 그 중 webroot plugin을 통해서 인증서를 발급해 보겠습니다.  

```
# 설치하기
wget https://dl.eff.org/certbot-auto
sudo mv certbot-auto /usr/local/bin/certbot-auto
sudo chown root /usr/local/bin/certbot-auto
sudo chmod 0755 /usr/local/bin/certbot-auto
sudo apt-get install certbot python3-certbot-nginx

# 의존성 설치 및 버전확인
certbot-auto --version
```

Webroot Plugin은 /.well-known이라는 폴더에 파일을 추가해서 Let’s Encrypt 서버가 접속할 수 있도록 합니다.  

우선, /etc/nginx/sites-available/에 있는 conf 파일들 중에서 추가하고자 하는 파일을 열어줍니다.  
저는 default conf 파일을 통해 설정해보도록 하겠습니다.

```
sudo vim /etc/nginx/sites-available/default


server {

    listen      80;
    listen [::]:80;
	
    server_name gayeonland.site;
    charset     utf-8;

    location ~ /.well-known {
		allow all;
    }

}
```

server 블록 파일 안을 다음과 같이 적어줍니다.  

```
sudo nginx -t
sudo service nginx reload
```

nginx의 문법을 확인하고 재시작 합니다.  
<br>
이제 server block에서 root설정이 어디인지 알아보고 인증서를 발급해보겠습니다.  
인증서를 발급하기 위해서는 webroot-path라는 부분에 server block의 root 경로를 지정해줘야 발급이 가능하다고 합니다.  

다음 명령어를 통해서 설정파일에 들어갑니다.  

```
vim /etc/nginx/sites-available/default
```

![aws install step](/assets/aws/domain9.JPG)

root 옆에 있는 경로를 기억해줍니다. **/var/www/html/**  
<br>
```
sudo certbot --nginx -d gayeonland.site -d www.gayeonland.site
```

SSL 인증을 회득하기 위해 다음 명령어를 입력합니다.  

![aws install step](/assets/aws/domain11.JPG)

위 사진처럼 **Congratulations!**가 뜬다면 SSL 인증을 회득한 것입니다.  
<br>

webroot plugin을 통해 잘 발급을 받았다면 아래와 같은 4개의 파일들이 생성됩니다.  
* cert.pem : 도메인의 인증서
* chain.pem : Let’s Encrypt chain 인증서
* fullchain.pem : cert.pem + chain.pem
* privkey.pem : 인증서의 개인키
<br>
```
sudo ls -l /etc/letsencrypt/live/gayeonland.site
```

해당 파일이 존재하는지 확인해봅니다.  
![aws install step](/assets/aws/domain12.JPG)
<br>
```
sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

보안을 위해서 디프-헬만 그룹을 추가해야합니다.    
화면에 여러 점들이 찍히면서 진행이되며 시간이 다소 몇분 소요됩니다.&#128164;  

![aws install step](/assets/aws/domain13.JPG)
<br>

Nginx의 server block들을 처리해서 TLS/SSL 설정을 해줍니다.  

```
sudo vim /etc/nginx/sites-available/default

#REMOVE
server{
    ...
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;
    ...
}    
```

이 폴더의 listen 부분을 지워줍니다.  

```
server{
    ...
    listen 443 ssl;

    server_name [domain] [another_domain];

    ssl_certificate /etc/letsencrypt/live/[domain]/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/[domain]/privkey.pem;

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    ssl_dhparam /etc/ssl/certs/dhparam.pem;
    ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA';
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:50m;
    ssl_stapling on;
    ssl_stapling_verify on;
    add_header Strict-Transport-Security max-age=15768000;
	...
}

server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}
```

default 파일을 다음과 같이 수정합니다.  

```
sudo nginx -t
sudo service nginx restart
or
sudo /etc/init.d/nginx restart
# sudo service nginx restart가 실행되지 않으면 위의 명령어로도 실행 가능합니다.
```

nginx의 문법을 확인한 뒤, 재실행 합니다.  

![aws install step](/assets/aws/step16.JPG)

https가 작동할 수 있도록, AWS에서 인바운드 규칙을 수정해줍니다.  

[https://www.ssllabs.com/ssltest/analyze.html?](https://www.ssllabs.com/ssltest/analyze.html) &#10140; 이 사이트를 들어가면 SSL 설정이 완료되었는지 확인하실 수 있습니다!&#128516;  

<br>
### 결과화면

![aws install step](/assets/aws/result4.png)

위의 사진처럼 주소창에 자물쇠 아이콘이 뜬다면 HTTPS 적용에 성공한 것입니다!&#128582;   


