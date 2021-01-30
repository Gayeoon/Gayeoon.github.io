---  
layout: post  
title: "PHP 설치"  
subtitle: "우분투 16.04LTS에 APM 소스 설치하기 - PHP 설치"  
categories: server
tags: ubuntu MySQL APM
comments: false  
---  

## PHP 설치

### &#128204; PHP 의존성 패키지 설치

```
apt-get install libxml2-dev
apt-get install libjpeg-dev
apt-get install libpng-dev
```

#### &#128226; TIP
{% raw %} /var/lib/dpkg/lock {% endraw %} 잠금파일을 얻을 수 없습니다.  
위의 오류는 잦은 강제 리부팅 등이 발생했을때 나타납니다.  
vm을 리부팅 하거나 {% raw %} /var/lib/dpkg/lock {% endraw %}삭제 후 다시시도 하면 해결됩니다.&#128521;
<br>
<hr>
### &#128204; PHP 설치 (configure, make, make install)

```
cd /usr/local
wget https://www.php.net/distributions/php-7.4.1.tar.gz
tar xvfz php-7.4.1.tar.gz
```

{% raw %}/usr/local{% endraw %}에 PHP를 다운받은 뒤 압축을 풀어줍니다.  

```
cd php-7.4.1
./configure \
--with-apx2=/usr/local/apache2.4/bin/apxs \
--with-mysql=/usr/local/mysql \
--with-mysqli=/usr/local/mysql/bin/mysql_config \
--with-imap-ssl \
--with-iconv \
--with-gd \
--with-jpeg \
--with-png \
--with-libxml \
--with-openssl
```

<br>
#### &#128161; ERROR &#128161;

![php install error](/assets/php/error1.JPG)

오류가 등장했습니다.. PHP7부터는 --with-mysql 옵션을 지원하지 않는다고 합니다..&#128581;  
`$ apt-get install libsqlite3-dev` 으로 libsqlite3-dev를 설치해준 뒤,  

[https://serverfault.com/questions/762616/compiling-php7-with-mysql-error](https://serverfault.com/questions/762616/compiling-php7-with-mysql-error)  
[https://jirak.net/wp/php-mysqlnd-mysql-native-driver/](https://jirak.net/wp/php-mysqlnd-mysql-native-driver/)  

```
./configure \
--with-apxs2=/usr/local/apache2.4/bin/apxs \
--enable-mysqlnd \
--with-mysql-sock=mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-imap-ssl \
--with-iconv \
--enable-gd \
--with-jpeg \
--with-libxml \
--with-openssl
```

위 링크의 configure 옵션을 참고하며 ./configure 옵션을 바꿔줍니다.  

![php install error](/assets/php/error2.JPG)

그런데 또 오류가 등장했습니다..&#128545; apache2.4 폴더를 못찾는 것 같으니 확인을 해봅니다.  

![php install error](/assets/php/error3.JPG)

왜그랬는지는 모르겠지만 apache2.4 폴더 이름을 apache24로 저장했었습니다! &#128561;  

```
./configure \
--with-apxs2=/usr/local/apache24/bin/apxs \
--enable-mysqlnd \
--with-mysql-sock=mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-imap-ssl \
--with-iconv \
--enable-gd \
--with-jpeg \
--with-libxml \
--with-openssl
```

원인을 찾았으니, configure의 apache 폴더를 apache24로 수정해서 다시 실행해줍니다.

![php install step](/assets/php/step1.JPG)

configure에 성공했습니다! &#128518;  


```
make -> 오래걸림
make test -> 더 오래걸림
make install -> 비교적 빨리 끝남
```

![php install step](/assets/php/step2.JPG)

make가 완료되면 위와 같은 메시지가 출력됩니다.  
메시지에도 적혀 있지만 **반드시 make test 명령어를 수행해줘야 합니다.**  

make install까지 마치면{% raw %} /usr/local/apache24/modules {% endraw %}디렉토리에서 
php 파일이 제대로 설치되었는지 확인할 수 있습니다.  

![php install step](/assets/php/step3.JPG)

확인해보면 libphp7.so 파일이 설치되어 있는 것을 확인할 수 있습니다.  
아파치는 DSO (Dynamic Shared Object : 동적 공유 객체 방식)으로 설치되어 있어서 
아파치를 컴파일한 상태에서 새로운 모듈이 추가될 때 새로 또 컴파일하지 않아도 됩니다.  
즉, httpd 에 기능이 포함되는 것이 아니라 외부에 기능을 두고 필요할때마다 동적으로 기능을 호출해서 사용하는 방식이라는 뜻입니다.  
PHP는 대부분 이러한 DSO 모듈방식을 사용합니다.  
참조링크:[https://victorydntmd.tistory.com/222](https://victorydntmd.tistory.com/222)  
<br>
<hr>
### &#128204; Apache와 PHP 연동

{% raw %}/usr/local/apache2.4/conf/httpd.conf {% endraw %}의 아파치 설정파일(httpd.conf)을 열어서 
PHP 모듈이 설치되어 있는지 확인해봅니다.  

![php install step](/assets/php/step4.png)

잘 설치되어 있다면 아파치 설정파일(httpd.conf)을 vi 편집기로 열어 mime_module에 AddType을 해줍니다.  

```
vi /usr/local/apache2.4/conf/httpd.conf

AddType application/x-httpd-php .php .html
```

<br>
<hr>
### &#128204; PHP.ini 파일 세팅

php.ini 는 php 의 설정파일입니다.
- php.ini-development(개발 시스템 용)과 php.ini-production(프로덕션 시스템용) 두 개의 파일이 있습니다.
- 개발용은 더 많은 오류와 경고를 표시하지만, 보안상의 이유로 개발환경에서만 사용해야 합니다.

![php install step](/assets/php/step5.JPG)

```
cd /usr/local/php-7.4.1
cp php.ini-production /usr/local/lib/php.ini
```

<br>
<hr>
### &#128204; 테스트를 위한 PHP 파일 작성

```
cd /usr/local/apache2.4/htdocs
vi phpinfo.php

<? php
phpinfo();
?>
```

http -k start 명령어로 아파치를 실행시킵니다.  
{ %raw } ps-ef| grep httpd { %endraw } 명령어로 아파치가 실행중인지 확인합니다.  

![php install step](/assets/php/step6.JPG)

```
sudo /usr/local/apache2.4/bin/httpd -k start
ps -ef|grep httpd
apt install curl
sudo curl http://127.0.0.1
```

<br>
<hr>
### 결과화면

![php result](/assets/php/result2.JPG)

![php result](/assets/php/result.JPG)

PHP 설치 정보가 출력된다면 PHP 설치 및 아파치와의 연동이 성공적으로 된 것입니다! &#128582;  

