---  
layout: post  
title: "배달 어플 DataBase ERD 설계하기"  
subtitle: "배달 어플 DataBase ERD 설계하기"  
categories: restful
tags: DB ERD Query
comments: false  
---  

## DataBase ERD 설계 및 쿼리 작성 - 1

### &#128204; 배달의민족 ERD 설계하기

#### &#128226; AQueryTool이란?  
&#10140; 무겁고 복잡, 불편한 기존 ERD 프로그램을 가볍고 깔끔, 간결 이쁘고 쓰기 좋게 만든 ERD 프로그램입니다.  
&#10140; AQueryTool 사이트 : [https://aquerytool.com/](https://aquerytool.com/)  

AQueryTool을 이용해서 배달의 민족 ERD를 작성해보겠습니다.&#128518;  

![DB](/assets/aws/db1.png)

**모든 테이블 생성 SQL**을 선택해서 작성한 ERD를 SQL문으로 바꿔줍니다.  

![DB](/assets/aws/db2.png)

외부접속 허용 Test를 마친 DataGrip의 SQL에 새로운 스키마를 만들어줍니다.  

![DB](/assets/aws/db4.JPG)

console 창에 Aquerytool을 이용하여 만든 SQL문을 넣어준 뒤, 실행을 돌립니다.  

![DB](/assets/aws/db3.JPG)

그러면 작성한 테이블이 추가되게 됩니다.

<br>
### 결과화면
![DB](/assets/aws/DeliveryApp.png)

<br>
<hr>
### &#128204; 네이밍 문법

#### &#127812; CamelCase  
&#10140; CamelCase란 단어가 합쳐진 부분마다 맨 처음 글자를 대문자로 표기하는 방법입니다.  
&#10140; 두 개 이상의 단어가 모인 합성어에서 사용됩니다. 쌍봉낙타의 등과 닮았다고 하여 CamelCase라는 이름이 붙었습니다.  

- lowerCamelCase  
	camelCase에서, 맨 앞글자를 소문자로 표기하는 것을 뜻합니다.  
	나머지 뒤에 따라붙는 단어들의 앞글자는 모두 대문자로 표기합니다.  
	*ex) ***n***amu***W***iki***R***eflec***B***eat***C***omponent, ***b***eat***M***ania,…*  

- UpperCamelCase (=PascalCase)  
	CamelCase에서, 맨 앞글자를 대문자로 표기하는 것을 뜻합니다. PascalCase라고도 불립니다.  
	나머지 뒤에 따라붙는 단어들의 앞글자는 모두 대문자로 표기합니다.  
	*ex) ***N***amu***W***iki***R***eflec***B***eat***C***omponent, ***B***eat***M***ania,…*  

#### &#127812; snake_case  
&#10140; snake_case란 단어가 합쳐진 부분마다 중간에 언더라인을 붙여 주는 방법입니다.  
&#10140; 일반적으로는 언더라인을 사용하나, 언더라인 대신 하이픈(-)을 써도 snake-case라고 할 수 있습니다.  

- Train_Case  
	Snake_Case에서, 각 단어의 맨 앞글자를 대문자로 표기하는 것을 뜻합니다.  
	*ex) ***V***isual_***S***tudio_***C***ommunity_2013, ***N***ot_***U***pper_***C***amel_***C***ase, …*  

- spinal_case  
	snake_case에서, 각 단어의 맨 앞글자를 소문자로 표기하는 것을 뜻합니다.  
	*ex) ***v***isual_***s***tudio_***c***ommunity_2013, ***n***ot_***l***ower_***c***amel_***c***ase, …*  
<br>
<hr>
### &#128204; 한글 인코딩 및 Timezone 설정하기

Query를 작성하기 전, 한글 입력을 위해 한글 인코딩 설정을 해줍니다.  
File &#10140; Settings &#10140; Editor &#10140; File Encodings  

![DB](/assets/aws/db5.png)

Encoding을 **UTF-8**로 변경해줍니다.  

![DB](/assets/aws/db6.JPG)

mysql에 time zone이 설정되어 있는지 확인합니다.  
위의 사진과 같다면 time zone이 설정되어 있지 않다는 것입니다.&#128581;  

```
SET GLOBAL time_zone='Asia/Seoul';
SET time_zone='Asia/Seoul';
```
다음 명령어들을 입력합니다.  

<br>
#### &#128161; ERROR &#128161;

![DB](/assets/aws/db7.JPG)

두 문장 다 안된다면 다른 방법으로 설정해야 합니다.  

```
SELECT b.name, a.time_zone_id
FROM mysql.time_zone a, mysql.time_zone_name b
WHERE a.time_zone_id = b.time_zone_id AND b.name LIKE '%Seoul';

SET time_zone='Asia/Seoul';
```

![DB](/assets/aws/db8.JPG)

그래도 안됩니다..&#128543;  
침착하게 [https://dev.mysql.com/downloads/timezones.html](https://dev.mysql.com/downloads/timezones.html) 이 링크에서 
Non POSIX with leap seconds를 다운받습니다.  

압축을 풀고 47000줄 정도의 sql을 mysql 스키마에 실행합니다.  
**&#128226; use mysql을 하고 실행해야합니다.**  
<br>
![DB](/assets/aws/db9.JPG)

성공했습니다.&#128079; 이제 아까 위에서 했던 명령어들을 다시 입력합니다.  

![DB](/assets/aws/db10.JPG)

mysql.cnf 파일에 `default-time-zone = Asia/Seoul` 문장을 추가해준뒤, mysql을 재실행합니다.  

![DB](/assets/aws/db10.JPG)

mysql server timezone 한국으로 설정하기 성공!&#128582;
