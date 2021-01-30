---  
layout: post  
title: "Apache 설치"  
subtitle: "우분투 16.04LTS에 APM 소스 설치하기 - Apache 설치"  
categories: server
tags: ubuntu Apache APM
comments: false  
---  

## 소스설치란?
&#10140; linux에서 소스를 컴파일하여 설치하는 것을 말합니다.  
- {% raw %}/usr/local{% endraw %}에 설치하는 것이 관례입니다.  
- 소스파일은 {% raw %}/usr/local/src{% endraw %}에 보관합니다.  
- 컴파일 설정을 마친 후에는 아래 명령어로 설치를 수행합니다.  

```
./configure
make
make install
```

- Apache &#10140; MySQL &#10140; PHP 설치 순으로 진행합니다.  
- 설치환경 : Ubuntu 16.04 64bit (VM)  
- Apache 2.4.25 / MySQL 8.0.19 / PHP 7.4.1  
<br>
<hr>
## Apache 설치

### &#128204; APR 설치 

```
wget http://archive.apache.org/dist/apr/apr-1.5.1.tar.gz  
tar xvfz apr-1.5.1.tar.gz
```

압축을 풀어준 다음에는 apr-1.5.1 디렉토리가 생성됩니다.


![apache install](/assets/apache/step6.JPG)


해당 디렉토리의 configure을 이용하여 설치를 진행합니다.  

관리자 권한이 필요하기 때문에 sudo를 붙여서 명령어를 입력합니다.

```
sudo ./configure --prefix=/usr/local/apr
```

<br>
#### &#128161; ERROR &#128161;

![apache install error](/assets/apache/error2.JPG)


libtolT라는 파일을 제거할 수 없다는 에러가 발생했지만 침착하게 cp libtool libtoolT 라고 명령어를 입력하여
libtool이라는 파일을 libtoolT라는 파일로 복사 해줍니다.  


![apache install error](/assets/apache/error3.JPG)


그리고 다시 sudo ./configure --prefix=/usr/local/apr 입력하여 설치를 시도합니다.  

![apache install error](/assets/apache/error4.JPG)

에러가 해결되었습니다!! &#128518;
<br><br>

```
sudo make
sudo make install  
```

설치가 성공적으로 진행되었으면 /usr/local 디렉토리에 다음과 같이 apr이 생깁니다. 

![apache install](/assets/apache/step4.JPG)

<br>

<hr>

### &#128204; APR-util 설치

```
wget http://archive.apache.org/dist/apr/apr-util-1.5.4.tar.gz  
tar xvfz apr-util-1.5.4.tar.gz  
```

압축을 풀어준 다음에는 apr-util-1.5.4 디렉토리가 생성됩니다.  

![apache install](/assets/apache/step5.JPG)

해당 디렉토리의 configure을 이용하여 apr과 같은 방식으로 설치를 진행합니다.  

```
sudo ./configure --prefix=/usr/local/apr-util  --with-apr=/usr/local/apr  

sudo make  
sudo make install  
-> sudo make && sudo make install로도 가능
```

<br>

<hr>

### &#128204; PCRE 설치

```
wget https://sourceforge.net/projects/pcre/files/pcre/8.39/pcre-8.39.tar.gz  
tar xvfz pcre-8.39.tar.gz  
```

압축을 풀어준 다음에는 pcre-8.39 디렉토리가 생성됩니다.  

![apache install](/assets/apache/step7.JPG)

역시나 해당 디렉토리의 configure을 이용하여 위와 같은 방식으로 설치를 진행합니다.    

```
sudo ./configure --prefix=/usr/local/pcre  

sudo make  
sudo make install  
-> sudo make && sudo make install로도 가능  
```

<br><br>
설치가 성공적으로 진행되었으면 /usr/local 디렉토리에 pcre이 생깁니다. 

![apache install](/assets/apache/step8.JPG)
<br>

<hr>

### &#128204; Apache httpd 2.4.25 설치

```
wget http://archive.apache.org/dist/httpd/httpd-2.4.25.tar.gz  
tar xvfz httpd-2.4.25.tar.gz  
```

httpd-2.4.25.tar.gz의 압축을 풀어줍니다.

![apache install](/assets/apache/step9.JPG)

```
sudo ./configure --prefix=/usr/local/apache24 
--with-apr=/usr/local/apr 
--with-apr-util=/usr/local/apr-util 
--with-pcre=/usr/local/pcre 
--enable-mods-shared=most 
--enable-module=so 
--enable-rewrite  
sudo make && sudo make install  
```

<br><br>
/usr/local/apache24 디렉토리로 들어가서 apachectl이 있다면 설치가 잘 된 것 입니다.   
 
![apache install](/assets/apache/step10.JPG)

```
cd /usr/local/apache24/bin
sudo cp apachectl /etc/init.d/httpd
```

<br><br>
/etc/init.d 에 들어가서 httpd 파일이 있는 것을 확인합니다.  

![apache install](/assets/apache/step11.JPG)

/usr/local/apache24/conf 에 들어가면 httpd.conf 파일을 볼 수 있는데 이 파일은 환경설정 파일입니다.

![apache install](/assets/apache/step12.JPG)

G 에디트를 이용하여 실행시킵니다.

```
cd /usr/local/apache24/conf
sudo gedit httpd.conf
```

![apache install](/assets/apache/step13.JPG)

servername www.example.com:80을 주석을 푼 뒤, localhost로 바꿔줍니다.

sudo service httpd start 를 입력하면 서비스가 시작되어야 합니다. &#128550;

<br>
#### &#128161; ERROR &#128161;

![apache install error](/assets/apache/error5.JPG)

하지만 서비스는 시작되지 않았고 에러만 발생했습니다..&#128545;

```
sudo apt install policycoreutils
sestatus
```

policycoreutils을 설치하면 SELinux status가 enable 상태에서 disable 상태로 바뀌게 됩니다.
sestatus 명령어로 상태를 다시 확인해 준 뒤, service httpd start를 해줍니다.
<br>

<hr>

### 결과화면

이제 브라우저에 localhost를 입력하면  it works! 메시지를 볼 수 있습니다.

![apache install](/assets/apache/result.JPG)

it works! 가 보인다면
apache2.4가 제대로 설치되었고 잘 작동되었다는 뜻입니다. &#128582;








