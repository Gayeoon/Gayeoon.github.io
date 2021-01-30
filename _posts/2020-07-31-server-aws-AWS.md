---  
layout: post  
title: "AWS 서버 구축하기 - APM 설치 및 외부접속 허용하기"  
subtitle: "AWS 서버 구축하기 - APM 설치 및 외부접속 허용하기"  
categories: server
tags: AWS Server NPM
comments: false  
--- 

## AWS 서버 구축 - 1

### &#128204; AWS 인스턴스 받기

[https://ap-northeast-2.console.aws.amazon.com/](https://ap-northeast-2.console.aws.amazon.com/)  
AWS 홈페이지에 가입합니다.  

서비스 -> EC2 -> 인스턴스 시작  

![aws install step](/assets/aws/step1.JPG)

Ubuntu 18.04를 선택해줍니다.    

![aws install step](/assets/aws/step3.JPG)

보안 그룹 구성에서 80포트를 사용할 수 있도록 규칙을 새로 추가해줍니다.  
새로운 **키 페어**를 다운받은 뒤 인스턴스를 시작해줍니다.  

![aws install step](/assets/aws/step2.JPG)

네트워크 및 보안 -> 탄력적 IP -> 탄력적 IP 주소 할당을 해주시면 IP주소가 할당이 됩니다.  
할당 받은 IP 주소에 방금 생성한 인스턴스를 연결해줍니다.  

![aws install step](/assets/aws/step4.JPG)

연결을 마치면 다음과 같이 인스턴스에 탄력적 IP가 매핑된 것을 확인할 수 있습니다.  
<br>
APM 설치를 위해 아래 사이트에서 winSCP를 설치해줍니다.  
[https://winscp.net/eng/download.php](https://winscp.net/eng/download.php)  

![aws install step](/assets/aws/step6.JPG)

AWS 인스턴스의 퍼블릭 IP를 호스트 이름에 넣고 사용자 이름을 설정해 준 뒤, 고급 사이트 설정을 들어갑니다.  

![aws install step](/assets/aws/step7.JPG)

고급 사이트 설정에서는 위에서 받은 **키페어 파일**을 넣을 수 있습니다.  
<br>
![aws install step](/assets/aws/step8.JPG)

이제 APM을 설치하면 됩니다! &#128521;

<br>
<hr>
### &#128204; nginx 설치
```
sudo apt update

sudo apt-get install nginx

```

nginx를 설치해준 뒤 `nginx -v` 명령어로 버전을 확인하여 잘 설치 되어 있는지 확인합니다.  

![aws install step](/assets/aws/step9.JPG)

<br>
<hr>
### &#128204; php 설치
```
sudo apt install php
sudo apt-get install php7.2-fpm

sudo vi /etc/nginx/sites-available/default
```

PHP와 nginx의 연동을 위해 default 파일을 수정해줍니다.  

![aws install step](/assets/aws/step11.JPG)

위의 사진과 같이 default 파일을 수정해 준 뒤, `sudo nginx -t`를 통해 문법에 문제가 있는지 확인해줍니다.  

문제가 없다면 `sudo service nginx restart`로 nginx를 재실행 해줍니다.  

<br>
### 결과화면

![aws install step](/assets/aws/step12.JPG)

phpinfo가 잘 출력되는 것을 확인 할 수 있습니다. &#128516;

![aws install result](/assets/aws/result1.jpg)

핸드폰 LTE로 해당 IP에 접속했을 때도 phpinfo를 확인하실 수 있습니다. &#128582;
<br>
<hr>
### &#128204; MySQL 설치
```
sudo apt-get install mysql-server-5.7

sudo mysql
update mysql.user set plugin='mysql_native_password' where user='root';
update mysql.user set authentication_string=PASSWORD('new_password') where user='root';
flush privileges;
quit
```

mysql 5.7을 설치해준 뒤, 관리자 권한으로 mysql에 접속하여 패스워드를 변경해줍니다.  


```
sudo service mysql restart
mysql -u root -p
```

mysql 서버를 재시작 해준 뒤, 새로운 패스워드로 mysql에 접속해봅니다.  
<br>
mysql을 외부접속이 가능하게 하기 위해 DataGrip을 설치해줍니다.   
[https://www.jetbrains.com/ko-kr/datagrip/download/#section=windows](https://www.jetbrains.com/ko-kr/datagrip/download/#section=windows)  
<br>
```
cd /etc/mysql/mysql.conf.d
sudo vi mysqld.cnf

port = 3306
bind-address = 127.0.0.1
```

127.0.0.1은 현재 Lock이 걸린 상태기 때문에, root 계정 이외의 접속자는 제한됩니다.  
MySQL의 설정 파일로 들어가서 port를 설정해주고 bind-address를 0.0.0.0으로 바꿔줍니다.  

`sudo /etc/init.d/mysql restart` 로 MySQL을 재시작 해줍니다.  

```
mysql -u root -p
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'Password';
FLUSH PRIVIEGES;

sudo service mysql restart
```

모든 외부 IP에게 원격 접속을 허용해 준 뒤, MySQL을 재실행 해줍니다.  

<br>
#### &#128161; ERROR &#128161;

![aws install error](/assets/aws/error1.JPG)

에러가 발생했습니다..&#128543;  
외부에서 접속하려면 명시적으로 데이터베이스의 이름을 써줘야 합니다.  
그렇기 때문에 Name &#10140; @3.34.167.226, Host &#10140; 3.34.167.226 로 변경해주면 해결됩니다.&#128582;   

하지만 그래도 에러는 사라지지 않습니다..&#128552;  

![aws install error](/assets/aws/error2.png)

이번엔 이런 에러가 발생했습니다.&#128545;	  
이 에러는 방화벽 문제이기 때문에 방화벽을 확인해줍니다.  

[https://m.blog.naver.com/varkiry05/198610727](https://m.blog.naver.com/varkiry05/198610727)  
[https://ckbcorp.tistory.com/532](https://ckbcorp.tistory.com/532)  

다음 블로그들을 참고해주세요!!  

<br>
### 결과화면

![aws install result](/assets/aws/result2.JPG)

저는 모든 에러를 해결하고 3일만에 겨우 외부 접속을 성공했습니당!! &#128518;

