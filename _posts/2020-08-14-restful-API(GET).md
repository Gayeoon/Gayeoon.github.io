---  
layout: post  
title: "배달 어플 API 구현 및 명세서 작성하기 - GET 방식 사용"  
subtitle: "배달 어플 API 구현 및 명세서 작성하기 - GET 방식 사용"  
categories: restful
tags: DB API PHP GET
comments: false  
---  

## API 구현 및 명세서 작성하기 - GET 방식 1

### &#127813; API 명세서 작성하기

![API Step](/assets/api/api.JPG)

위의 명세서를 기반으로 API 개발을 시작하겠습니다.&#128518;  
<br>
<hr>
### &#127848; User 모든 정보 출력 API

```
$r->addRoute('GET', '/users', ['IndexController', 'getUsers']);
```

Index.php에 라우팅 정의  

```
/*
 * API No. 1
 * API Name : User 모든 정보 출력 API
 * 마지막 수정 날짜 : 20.08.14
 */
case "getUserInfo":
    http_response_code(200);

    $keyword = $_GET['userId'];

    if(!isValidID($keyword)){
		$res->isSuccess = FALSE;
		$res->code = 200;
		$res->message = "유효하지 않은 ID입니다.";
		echo json_encode($res, JSON_NUMERIC_CHECK);
		break;
    }

    $res->result = getUserInfo($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function getUserInfo($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT * FROM User WHERE UserId = ? AND IsDeleted = 'N';";

    $st = $pdo->prepare($query);
    //    $st->execute([$param,$param]);
    $st->execute([$keyword]); //파라미터 list 형태로 넣을 것
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res;
}

function isValidId($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT EXISTS (SELECT * FROM User WHERE UserId = ? AND IsDeleted = 'N') AS exist;";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    //    $st->execute();
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;
    //echo json_encode($res);
    return intval($res[0]['exist']);
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api1.JPG)

![API Step](/assets/api/api2.JPG)
<br>
<hr>
### &#127831; MY배민 페이지의 User 정보 출력 API

```
$r->addRoute('GET', '/user/{user_id}', ['IndexController', 'getUser']);
```

Index.php에 라우팅 정의  

```
/*
 * API No. 2
 * API Name : MY배민 페이지의 User 정보 출력 API
 * 마지막 수정 날짜 : 20.08.14
 */
case "getUser":
    http_response_code(200);

    $keyword = $_GET['userId'];

    if(!isValidID($keyword)){
		$res->isSuccess = FALSE;
		$res->code = 200;
		$res->message = "유효하지 않은 ID입니다.";
		echo json_encode($res, JSON_NUMERIC_CHECK);
		break;
    }

    $res->result = getUser($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function getUser($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT User.Name, User.Level, COUNT(*) AS Coupon
		FROM User
		JOIN Coupon
		ON User.UserIdx= Coupon.UserIdx AND Coupon.isUsed = 'N'
		WHERE UserId = ? AND isDeleted = 'N'
		GROUP BY User.UserIdx;";

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

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api3.JPG)

![API Step](/assets/api/api4.JPG)
<br>
<hr>
### &#127815; 포인트 이용 내역 조회 API

```
$r->addRoute('GET', '/user/{user_id}/point', ['IndexController', 'getUserPoint']);
```

Index.php에 라우팅 정의  

```
/*
 * API No. 3
 * API Name : 포인트 이용 내역 조회 API
 * 마지막 수정 날짜 : 20.08.14
 */
case "getUserPoint":
    http_response_code(200);

    $keyword = $_GET['userId'];

    if(!isValidID($keyword)){
		$res->isSuccess = FALSE;
		$res->code = 200;
		$res->message = "유효하지 않은 ID입니다.";
		echo json_encode($res, JSON_NUMERIC_CHECK);
		break;
    }

    $res->result = getUserPoint($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function getUserPoint($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT Point, date_format(Date,'%Y-%m-%d') AS DATE , EndDate, Point.IsDeleted As Used, Store
		FROM Point
		JOIN User ON User.UserId = ? AND Point.UserIdx = User.UserIdx
        ORDER BY Date DESC;
        ";

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

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api5.JPG)

![API Step](/assets/api/api6.JPG)
<br>
<hr>
### &#127826; 보유 포인트 합계 조회 API

```
 $r->addRoute('GET', '/user/{user_id}/point/sum', ['IndexController', 'getUserPointSum']);
```

Index.php에 라우팅 정의  

```
/*
 * API No. 4
 * API Name : 보유 포인트 합계 조회 API
 * 마지막 수정 날짜 : 20.08.14
 */
case "getUserPointSum":
    http_response_code(200);

    $keyword = $_GET['userId'];

    if(!isValidID($keyword)){
		$res->isSuccess = FALSE;
		$res->code = 200;
		$res->message = "유효하지 않은 ID입니다.";
		echo json_encode($res, JSON_NUMERIC_CHECK);
		break;
    }

    $res->result = getUserPointSum($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function getUserPointSum($keyword)
{
    $pdo = pdoSqlConnect();

    $query = "SELECT CONCAT(sum(Point), '원') AS Sum FROM Point
        JOIN User ON UserId = ?
        WHERE User.UserIdx = Point.UserIdx AND Point.IsDeleted <= 0
        GROUP BY UserID;
        ";

    $st = $pdo->prepare($query);
    //    $st->execute([$param,$param]);
    $st->execute([$keyword]); //파라미터 list 형태로 넣을 것
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res[0];
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api7.JPG)

![API Step](/assets/api/api8.JPG)
<br>
<hr>
### &#127839; 찜한가게, 바로결제, 전화주문 목록 조회 API

```
$r->addRoute('GET', '/user/{user_id}/choose', ['IndexController', 'getUserChoose']);
```

Index.php에 라우팅 정의  

```
/*
 * API No. 5
 * API Name : 찜한가게, 바로결제, 전화주문 목록 조회 API
 * 마지막 수정 날짜 : 20.08.14
 */
case "getUserChoose":
    http_response_code(200);

    $keyword = $_GET['userId'];
    $flag = $_GET['flag'];

    if(!isValidId($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($flag > 2){
		$res->isSuccess = FALSE;
        $res->code = 300;
        $res->message = "유효하지 않은 flag입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getUserChoose($keyword, $flag);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function getUserChoose($keyword, $flag)
{
    $pdo = pdoSqlConnect();

    if($flag == 2) {
        $query = "SELECT Store.Name, Store.Star, Store.Min, Store.Represent, Store.IsOpen, Store.IsOrder
        FROM Choose
        JOIN User on User.UserId = ?
        JOIN  Store
        ON Choose.UserIdx = User.UserIdx AND Choose.StoreIdx = Store.StoreIdx;";
    }
    else {
        $query = "SELECT Store.Name, Store.Star, Store.Min, Store.Represent, Store.IsOpen, Store.IsOrder
        FROM OrderMenu
        JOIN User on User.UserId = ?
        JOIN  Store
        ON OrderMenu.UserIdx = User.UserIdx AND OrderMenu.Payment = ? AND OrderMenu.StoreIdx = Store.StoreIdx;";
    }

    $st = $pdo->prepare($query);
    $st->execute([$keyword, $flag]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api9.JPG)

![API Step](/assets/api/api10.JPG)
<br>
<hr>
### &#127867; Store 상세 정보 출력 API

```
$r->addRoute('GET', '/store/{store_id}', ['IndexController', 'getStore']);
```

Index.php에 라우팅 정의  

```
/*
 * API No. 6
 * API Name : Store 상세 정보 출력 API
 * 마지막 수정 날짜 : 20.08.14
 */
case "getStore":
    http_response_code(200);

    $keyword = $_GET['storeId'];

    if(!isValidStoreId($keyword)){
		$res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
		break;
    }

    $res->result = getStore($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function getStore($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT Store.Name, Star, Min,  Type, CONCAT(FORMAT(Store.Tip , 0), '원') AS Tip, Store.Phone, Store.DeliveryTime, Explan
		FROM Store
		WHERE Store.StoreID = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    $res = array_filter($res[0]);
    return $res;
}

function isValidStoreId($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT EXISTS (SELECT * FROM Store WHERE StoreId = ? AND IsDeleted = 'N') AS exist;";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    //    $st->execute();
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;
    //echo json_encode($res);
    return intval($res[0]['exist']);
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api11.JPG)

![API Step](/assets/api/api12.JPG)
<br>
<hr>
### &#127823; Store 최근리뷰 / 사장님 댓글 개수 조회 API

```
$r->addRoute('GET', '/store/{store_id}/review-cnt', ['IndexController', 'getStoreReview']);
```

Index.php에 라우팅 정의  

```
/*
 * API No. 7
 * API Name : Store 최근리뷰 / 사장님 댓글 개수 조회 API
 * 마지막 수정 날짜 : 20.08.14
 */
case "getStoreReview":
    http_response_code(200);

    $keyword = $_GET['storeId'];

    if(!isValidStoreId($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getStoreReview($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function getStoreReview($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT COUNT(*) AS Review, COUNT(Review.Comment) AS Comments
		FROM Store
		JOIN Review
        ON Review.StoreIdx = Store.StoreIdx
        WHERE StoreId = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res[0];
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api13.JPG)

![API Step](/assets/api/api4.JPG)

