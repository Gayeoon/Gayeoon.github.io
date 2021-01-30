---  
layout: post  
title: "MySQL 설치"  
subtitle: "우분투 16.04LTS에 APM 소스 설치하기 - MySQL 설치"  
categories: ubuntu
tags: Ubuntu MySQL APM
comments: false  
---  
## MySQL 설치

### &#128204; MySQL 설치 (cmake, make, make install)

```
sudo apt-get update
sudo apt-get install cmake
sudo apt-get install libssl-dev
sudo apt-get install libboost-all-dev
sudo apt-get install libncurses5-dev libncursesw5-dev
```

apt-get을 update 해준 뒤, 필요한 패키지들을 설치합니다.   

```
cd /usr/local
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.19.tar.gz
tar xvfz mysql-8.0.19.tar.gz
```

mysql을 다운받고 압축을 풀어줍니다.  

```
cd /usr/local/mysql-8.0.19
cmake \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DMYSQL_DATADIR=/usr/local/mysql/data \
-DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock \
-DMYSQL_TCP_PORT=3306 \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DSYSCONFDIR=/etc \
-DWITH_EXTRA_CHARSETS=all \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_ARCHIVE_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DDOWNLOAD_BOOST=1 \
-DWITH_BOOST=/usr/local/mysql/boost 
```

cmake 명령어를 통해 설치 옵션을 부여해줍니다.  

<br>
#### &#128161; ERROR &#128161;

![mysql install error](/assets/mysql/error3.JPG)

소스 디렉토리 내에 build를 위한 디렉토리를 추가 생성하고 그 하단에서 작업을 진행하도록 ~~(강제)~~**권고** 되어 버렸기 때문에 
해당 오류가 발생했습니다.  

```
rm -f CMakeCache.txt
mkdir thismysql
cd thismysql
cmake \
.. \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DMYSQL_DATADIR=/usr/local/mysql/data \
-DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock \
-DMYSQL_TCP_PORT=3306 \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DSYSCONFDIR=/etc \
-DWITH_EXTRA_CHARSETS=all \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_ARCHIVE_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DDOWNLOAD_BOOST=1 \
-DWITH_BOOST=/usr/local/mysql/boost 
```

thismysql이라는 build가 진행될 하위 폴더를 생성하고 그 곳에서 cmake 옵션을 부여해줍니다.  

```
-- Build files have been written to: /usr/local/mysql-8.0.19/thismysql
```

다음과 같은 메세지가 출력되면 cmake로 빌드가 완료된 것입니다.   

```
sudo make
sudo make install
```

혹시 오류가 날 수 있으니 두번으로 나눠서 make 명령어를 입력합니다.  
make는 시간이 상당히 오래 걸립니다. &#128164;  

<br>
#### &#128161; ERROR &#128161;

![mysql install error](/assets/mysql/error4.JPG)

또 에러가 발생했습니다. &#128563; 이 에러는 시스템의 메모리가 부족해서 발생하는 것이기 때문에 
가상머신의 RAM 크기를 4GB로 늘려준 뒤 다시 make를 시도합니다.  

![mysql install step](/assets/mysql/step1.JPG)

3시간만에 감동적인 성공ㅠㅠ &#128557;
<br>
<hr>
### &#128204; MySQL 데이터베이스 초기화

```
groupadd mysql
useradd -r -g mysql -s /bin/false mysql
```

시스템에 mysql 실행에 필요한 사용자 그룹과 유저가 없는 경우 만들어줍니다.  

```
cd /usr/local/mysql
mkdir mysql-files

chown -R mysql:mysql /usr/local/mysql
chown mysql:mysql mysql-files
chmod 750 mysql-files
```

새로운 디렉토리를 생성해주고 권한을 수정해줍니다.
* chown - 파일의 소유권자를 변경하는 명령어  
chown {소유권자}:{그룹식별자} {소유권을 변경하고 싶은 파일명} 형태로 명령어 실행  

* chmod - 대상 파일과 디렉토리의 사용권한을 변경할 때 사용  
chmod [옵션] [모드] [파일] 형태로 명령어 실행  
- 소유자권한 : 읽기, 쓰기, 실행  
- 그룹권한 : 읽기, 실행  
- 전체권한 : 없음  

![mysql install step](/assets/mysql/step2.JPG)

mysql-files의 권한이 drwxr-x---로 변경된 것을 확인할 수 있습니다.  
 
```
bin/mysqld --initialize --user=mysql \
--basedir=/usr/local/mysql \
--datadir=/usr/local/mysql/data

bin/mysql_ssl_rsa_setup
```

<br>
기본 데이터베이스를 생성해줍니다.  
데이터 디렉토리를 초기화하면 임시 비밀번호가 생성됩니다.  

![mysql install step](/assets/mysql/step3.JPG)

**vMiAy-U/a0p=**  
&#10003; mysql 서버에 연결할 때 **꼭** 필요하므로 기억해야합니다!

```
bin/mysqld_safe --user=mysql &
```

mysql 서버를 실행시킵니다.

![mysql install step](/assets/mysql/step4.JPG)

"{% raw %} mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql/data {% endraw %}" 라는 문구가 출력된다면 
정상적으로 실행이 되고 있는 것입니다. &#128521;  

```
bin/mysql -u root -p
-> 임시 비밀번호 입력
```

mysql 명령어로 서버에 연결해줍니다.
(위에서 출력되었던 임시비밀번호를 입력해야 합니다.)

```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'root-password';
```

root-password로 임시비밀번호를 변경할 수 있습니다.  
mysql에서 나오려면 **q**를 입력하시면 됩니다.  
mysql 서버를 종료하는 방법은 {% raw %} bin/mysqladmin -u root -p shutdown {% endraw %} 입니다.
<br>
<hr>
### &#128204; MySQL 서비스등록

```
sudo cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
sudo vi /etc/init.d/mysqld

basedir=/usr/local/mysql

datadir=/usr/local/mysql/data

update-rc.d mysqld defaults
```

MySQL 서비스를 등록해놓으면 서버가 실행되었을 때, MySQL이 자동으로 실행됩니다.  
mysqld 파일을 복사한 뒤 basedir과 datadir을 설정합니다.
<br>
<hr>
### &#128204; 환경설정

```
vi /etc/my.cnf

[mysqld]
bind-address=0.0.0.0
port=3306
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
```

MySQL의 옵션파일은 프로그램을 실행할때마다 명령줄에 옵션을 입력할 필요가 없도록 해줍니다.  
리눅스 운영체제에서 my.cnf 파일은 default로 {% raw %}/etc/my.cnf{% endraw %} 경로에서 읽어옵니다.
<br>
<hr>
### 결과화면

![mysql install result](/assets/mysql/result.JPG)

실행중인 프로세스를 확인하면 mysql이 실행되어 있는 것을 확인할 수 있습니다.  

![mysql install result](/assets/mysql/result2.JPG)

MySQL의 버전을 확인해봅니다.
  
![mysql install result](/assets/mysql/result4.JPG)

service mysql start로 MySQL을 실행시킨 뒤 서버의 상태를 확인해 봅니다.  
MySQL이 성공적으로 설치된 것 같습니다..!! &#128582;  

