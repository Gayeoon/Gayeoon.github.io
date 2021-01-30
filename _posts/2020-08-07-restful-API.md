---  
layout: post  
title: "간단한 API 구현하기"  
subtitle: "간단한 API 구현하기"  
categories: restful
tags: DB API PHP
comments: false  
---  

## API 설계하기

### &#128204; PHP 연동

PhpStorm을 다운로드 해줍니다.  
[https://www.jetbrains.com/phpstorm/download/download-thanks.html?platform=windows](https://www.jetbrains.com/phpstorm/download/download-thanks.html?platform=windows)  

DataGrip의 Database 외부 접속 연결과 같은 방식으로 DB를 연동해줍니다.
<br>
Tools &#10140; Deployment &#10140; Configuration  
PHP와 서버를 연결하기 위해 Deployment에 들어갑니다.  

![API Step](/assets/api/php1.JPG)

Host에 서버 IP를 입력한 뒤, Key pair를 넣어주고 **Test Connection**을 진행해줍니다.  
<br>
![API Step](/assets/api/php2.JPG)

성공적으로 연결이 되었다면 Browse Remote Host를 통해 연결된 폴더를 확인하실 수 있습니다.  
<br>
![API Step](/assets/api/php3.JPG)

Root path를 `/var/www/html/api-server`로 변경해준 뒤 index.php로 들어갑니다.  
저장된 프레임워크 사용을 위해 설정을 진행해줍니다.  
<br>
```
sudo vi /etc/nginx/sites-available/default
sudo service nginx restart
```
default 파일을 밑의 사진과 같이 수정해줍니다.  

![API Step](/assets/api/php4.JPG)

![API Step](/assets/api/php5.JPG)

수정사항을 저장한 뒤, nginx를 재실행 해줍니다.  

```
<?php
echo "hello world";
```
설정사항이 잘 적용 됐는지 확인하기 위해 위와같이 index.php를 수정하고 서버에 올려줍니다.

<br>
### 결과화면
![API Step](/assets/api/php6.JPG)

잘 적용 되었습니다!&#128582;
<br>
<hr>
### &#128204; User 목록 조회 API 구현
```
apt-get install composer
composer install
```
composer을 설치해줍니다.  

API 요청을 좀 더 쉽게 처리할 수 있도록 Postman을 설치합니다.  
[https://www.postman.com/downloads/](https://www.postman.com/downloads/)  

![API Step](/assets/api/php8.JPG)

Collection을 만들어 줍니다.  

![API Step](/assets/api/php9.JPG)

IP (저는 Domain으로 했습니다)를 검색해보시면 아까 작성했던 index.php 파일이 출력되는 것을 확인하실 수 있습니다.  

![API Step](/assets/api/php10.JPG)

이제 주석을 풀고 라우터 기능을 구현한 index.php를 서버에 올려줍니다.  

<br>
#### &#128161; ERROR &#128161;

![API Step](/assets/api/error1.JPG)

Ar... 안됩니다..&#128551;  
일단 적혀있는대로 /var/www/html/api-server/controllers/IndexController.php의 10번째 줄을 확인해봅니다.  

```
9	try {
10	addAccessLogs($accessLogs, $req);
11	switch ($handler){
...
```

10번째 줄을 주석처리 해줍니다.  

![API Step](/assets/api/php11.JPG)

해결되었습니다!! &#128518;
<br>
Test를 위해 DB 테이블을 하나 작성해줍니다.  

![API Step](/assets/api/db1.JPG)

그리고 이 테이블에 더미 데이터들을 추가해줍니다.

![API Step](/assets/api/db2.JPG)
<br>
```
$r->addRoute('GET', '/users', ['IndexController', 'getUsers']);

case "getUsers":
	http_response_code(200);
	$res->isSuccess = TRUE;
	$res->code = 100;
	$res->message = "테스트 성공";
	echo json_encode($res, JSON_NUMERIC_CHECK);
 	break;
```

PHP에 위의 코드를 추가해줍니다.  

![API Step](/assets/api/php12.JPG)

/users API 호출에 성공했는지 확인하실 수 있습니다.  

```
$res->result = getUsers();
```

IndexController.php에 `case "getUsers": ` 부분에 위의 코드를 추가해줍니다.  
결과값으로 getUsers() 함수 시행 결과를 리턴해주겠다는 의미입니다.  

```
function getUsers()
{
    $pdo = pdoSqlConnect();
    $query = "select * from testTable;";

    $st = $pdo->prepare($query);
    //    $st->execute([$param,$param]);
    $st->execute();
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res;
}
```

IndexPdo.php 파일에 다음과 같은 코드를 추가해줍니다.  
<br>
### 결과화면
![API Step](/assets/api/php13.JPG)

간단하게 user 목록을 조회하는 API를 구현해봤습니다.&#128582;
<br>
<hr>
### &#128204; User 상세 정보 조회 API 구현

```
$r->addRoute('GET', '/users/{no}', ['IndexController', 'getUserDetail']);
```

index.php에서 user의 상세 정보를 조회하는 라우팅을 정의해줍니다.  

```
case "getUserDetail":
	http_response_code(200);
	$res->result = getUserDetail($vars["no"]);
	$res->isSuccess = TRUE;
	$res->code = 100;
	$res->message = "테스트 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```
IndexController.php에서 관련 포맷을 정의해줍니다.  

```
function getUserDetail($no)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT * FROM testTable WHERE no = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$no]); // 여기가 ? 변수, 꼭 리스트 안에 넣을것!
    //    $st->execute();
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res[0];
}
```

IndexPdo.php에서 Query를 통해 데이터를 가져오는 작업을 해줍니다.  
<br>
### 결과화면
![API Step](/assets/api/php14.JPG)

User의 2번 항목을 가져오는 API를 구현해봤습니다.&#128582;
<br>
<hr>
### &#128204; Validation 처리

```
case "getUserDetail":
	http_response_code(200);

	$no = $vars["no"];

	if(!isValidNo($no)){
		$res->isSuccess = FALSE;
		$res->code = 100;
		$res->message = "유효하지 않은 no입니다.";
		echo json_encode($res, JSON_NUMERIC_CHECK);
		break;
	}

	$res->result = getUserDetail($vars["no"]);
	$res->isSuccess = TRUE;
	$res->code = 100;
	$res->message = "테스트 성공";
	echo json_encode($res, JSON_NUMERIC_CHECK);
	break;
```
IndexController에서 $no가 없을 경우 에러처리를 해주는 코드를 작성해줍니다.  

```
function isValidNo($no)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT EXISTS (SELECT * FROM testTable WHERE no = ?) AS exist;";

    $st = $pdo->prepare($query);
    $st->execute([$no]); // 여기가 ? 변수, 꼭 리스트 안에 넣을것!
    //    $st->execute();
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;
    //echo json_encode($res);
    return intval($res[0]['exist']);
}
```

IndexPdo에서 Validation을 체크하는 함수를 작성해줍니다.

<br>
### 결과화면
![API Step](/assets/api/php15.JPG)

유효하지 않은 번호가 들어왔을때 에러처리를 하는 API를 구현했습니다.&#128516;  
<br>
<hr>
### &#128204; Query String API 구현

```
case "getUsers":
    http_response_code(200);

    $keyword = $_GET['keyword'];

    $res->result = getUsers($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "테스트 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController에서 getUsers 부분에 keyword를 파라미터로 받아오는 코드를 추가해줍니다.  

```
function getUsers($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "select * from testTable where name like concat('%', ?, '%');";

    $st = $pdo->prepare($query);
    //    $st->execute([$param,$param]);
    $st->execute([$keyword]); //파라미터 list 형태로 넣을 것
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res;
}
```

IndexPdo에서 getUsers 함수의 query와 execute 부분을 수정해줍니다.  
<br>
### 결과화면
![API Step](/assets/api/php16.JPG)

Query String으로 검색 파라미터를 받아오는 API를 구현했습니다.&#128516;
<br>
<hr>
### &#128204; POST 방식

```
$r->addRoute('POST', '/user', ['IndexController', 'createUser']);
```

POST 방식은 일반적으로 생성하는 규칙입니다.  
index.php에서 user를 입력하여 회원가입을 진행하는 라우팅을 정의해줍니다.  

```
case "createUser":
    http_response_code(200);
    $res->result = createUser($req->name);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "테스트 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController에서 body를 읽어오는 코드를 작성해줍니다.  

```
function createUser($name)
{
    $pdo = pdoSqlConnect();
    $query = "INSERT INTO testTable (name) VALUES (?);";

    $st = $pdo->prepare($query);
    $st->execute([$name]);

    $st = null;
    $pdo = null;
}
```

IndexPdo에서 읽어온 value 값을 가지고 Table에 추가하는 코드를 작성해줍니다.  
<br>
### 결과화면
![API Step](/assets/api/php17.JPG)
![API Step](/assets/api/php18.JPG)

DB를 확인해보시면 Table에 summer 값이 추가된 것을 확인해보실 수 있습니다.&#128582;  




