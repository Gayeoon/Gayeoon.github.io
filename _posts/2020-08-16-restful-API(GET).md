---  
layout: post  
title: "배달 어플 API 구현 및 명세서 작성하기 - GET 방식 사용"  
subtitle: "배달 어플 API 구현 및 명세서 작성하기 - GET 방식 사용"  
categories: restful
tags: DB API PHP GET
comments: false  
---  

## API 구현 및 명세서 작성하기 - GET 방식 2

### &#127825; Store 찜 개수 조회 API

```
$r->addRoute('GET', '/store/{store_id}/choose-cnt', ['IndexController', 'getStoreChoose']);
```

Index.php에 라우팅 정의  

```
/*
 * API No. 8
 * API Name : Store 찜 개수 조회 API
 * 마지막 수정 날짜 : 20.08.14
 */
case "getStoreChoose":
    http_response_code(200);

    $keyword = $_GET['storeId'];

    if(!isValidStoreId($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getStoreChoose($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function getStoreChoose($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT COUNT(*) As Choose
        FROM Store
        JOIN Choose
        ON Choose.StoreIdx = Store.StoreIdx
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

![API Step](/assets/api/api15.JPG)

![API Step](/assets/api/api16.JPG)
<br>
<hr>
### &#127833; Store 메뉴 목록 조회 API

```
$r->addRoute('GET', '/store/{store_id}/menu', ['IndexController', 'getStoreMenu']);
```

Index.php에 라우팅 정의  

```
/*
 * API No. 9
 * API Name : Store 메뉴 목록 조회 API
 * 마지막 수정 날짜 : 20.08.14
 */
case "getStoreMenu":
    http_response_code(200);

    $keyword = $_GET['storeId'];

    if(!isValidStoreId($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getStoreMenu($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function getStoreMenu($keyword)
{
    $pdo = pdoSqlConnect();

    $query = "SELECT Menu.Name AS MenuName, CONCAT(FORMAT(Menu.Price , 0), '원') AS Price, Menu.Picture, Menu.IsPossible, Menu.MenuNum
        FROM Menu
        JOIN  Store
        ON Store.StoreID = 'mmmm' AND Menu.StoreIdx = Store.StoreIdx;";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api17.JPG)

![API Step](/assets/api/api18.JPG)
<br>
<hr>
### &#127855; User 주문 내역 목록 조회 API

```
$r->addRoute('GET', '/user/{user_id}/order', ['IndexController', 'getUserOrder']);
```

Index.php에 라우팅 정의  

```
/*
 * API No. 10
 * API Name : User 주문 내역 목록 조회 API
 * 마지막 수정 날짜 : 20.08.14
 */
case "getUserOrder":
    http_response_code(200);

    $keyword = $_GET['userId'];

    if(!isValidId($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getUserOrder($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function getUserOrder($keyword)
{
    $pdo = pdoSqlConnect();

    $query = "SELECT OrderMenu.Type,
    CASE
        WHEN TIMESTAMPDIFF(DAY, OrderMenu.Date, NOW()) < 1 THEN CONCAT('오늘')
        WHEN TIMESTAMPDIFF(DAY, OrderMenu.Date, NOW()) <= 7 THEN CONCAT(TIMESTAMPDIFF(DAY, OrderMenu.Date, NOW()), '일 전')
        ELSE CONCAT(date_format(OrderMenu.Date,'%m/%d'), ' (', SUBSTR( _UTF8'일월화수목금토', DAYOFWEEK(OrderMenu.Date), 1),')')
        END AS Date,
        Store.Category, Store.Name, OrderMenu.IsReview, CONCAT(FORMAT(Store.Tip , 0), '원') AS Tip,
               GROUP_CONCAT(Menu.Name) AS MenuName, CONCAT(FORMAT(SUM(Menu.Price) , 0), '원') AS MenuPrice, Store.StoreId, OrderMenu.OrderNumber, OrderMenu.OrderIdx
        FROM OrderMenu
        JOIN User on User.UserId = 'judy'
        JOIN  Store
        ON OrderMenu.UserIdx = User.UserIdx AND OrderMenu.Type <= 1 AND OrderMenu.StoreIdx = Store.StoreIdx
        JOIN  OrderMenuList
        ON OrderMenu.OrderNumber = OrderMenuList.OrderNumber
        JOIN  Menu
        ON Menu.MenuNum = OrderMenuList.MenuNum
        GROUP BY OrderMenu.Date, OrderMenu.OrderIdx
        ORDER BY OrderMenu.Date DESC ;";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api19.JPG)

![API Step](/assets/api/api20.JPG)
<br>
<hr>
### &#127814; User 상세 주문 내역 정보 조회 API

```
$r->addRoute('GET', '/user/{order_num}/order-detail', ['IndexController', 'getUserOrderDetail']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 11
* API Name : User 상세 주문 내역 정보 조회 API
* 마지막 수정 날짜 : 20.08.14
*/
case "getUserOrderDetail":
    http_response_code(200);

    $keyword = $_GET['orderNum'];

    if(!isValidNumber($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 주문 번호입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getUserOrderDetail($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;

```

IndexController.php에 관련 포맷 정의  

```
function getUserOrderDetail($keyword)
{
    $pdo = pdoSqlConnect();

    $query = "SELECT
       CASE
           WHEN HOUR(OrderMenu.Date) < 12 THEN date_format(OrderMenu.Date,'%Y년 %m월 %d일 오전 %h:%i')
           ELSE date_format(OrderMenu.Date,'%Y년 %m월 %d일 오후 %h:%i')
       END AS Date,
        OrderMenu.OrderNumber,  Store.Name, CONCAT(FORMAT(Store.Tip , 0), '원') AS Tip, OrderMenu.IsDelivered, Store.Phone, OrderMenu.Payment, OrderMenu.Address, Store.StoreId
        FROM OrderMenu
        JOIN  Store
        ON OrderMenu.OrderNumber = ? AND OrderMenu.StoreIdx = Store.StoreIdx;";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    $res = array_filter($res[0]);
    return $res;
}


function isValidNumber($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT EXISTS (SELECT * FROM OrderMenu WHERE OrderNumber = ?) AS exist;";

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

![API Step](/assets/api/api21.JPG)

![API Step](/assets/api/api22.JPG)
<br>
<hr>
### &#127841; User 주문 내역 메뉴 정보 조회 API

```
$r->addRoute('GET', '/user/{user_id}/order/menu', ['IndexController', 'getUserOrderMenu']);
```

Index.php에 라우팅 정의  

```
 /*
* API No. 12
* API Name : User 주문 내역 메뉴 정보 조회 API
* 마지막 수정 날짜 : 20.08.17
*/
case "getUserOrderMenu":
    http_response_code(200);

    $keyword = $_GET['userId'];

    if(!isValidId($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getUserOrderMenu($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;

```

IndexController.php에 관련 포맷 정의  

```
function getUserOrderMenu($keyword)
{
    $pdo = pdoSqlConnect();

    $query = "SELECT Menu.Name, CONCAT(FORMAT(Menu.Price , 0), '원') AS MenuPrice, OrderMenu.MenuOption, OrderMenuList.MenuCnt
        FROM OrderMenu
        JOIN User on User.UserId = ?
        JOIN  OrderMenuList
        ON OrderMenu.UserIdx = User.UserIdx AND OrderMenu.OrderNumber = 'B0QE01AX5S' AND OrderMenuList.OrderNumber = OrderMenu.OrderNumber
        JOIN  Menu
        ON Menu.MenuNum = OrderMenuList.MenuNum;";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    for($i=0; $i<sizeof($res); $i++)
    {
        $res[$i] = array_filter($res[$i]);
    }

    return $res;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api23.JPG)

![API Step](/assets/api/api24.JPG)
<br>
<hr>
### &#127817; User 리뷰 개수 조회 API

```
$r->addRoute('GET', '/user/{user_id}/review/count', ['IndexController', 'getUserReviewCount']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 13
* API Name : User 리뷰 개수 조회 API
* 마지막 수정 날짜 : 20.08.17
*/
case "getUserReviewCount":
    http_response_code(200);

    $keyword = $_GET['userId'];

    if(!isValidId($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getUserReviewCount($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function getUserReviewCount($keyword)
{
    $pdo = pdoSqlConnect();

    $query = "SELECT COUNT(*) AS MyReview
        FROM Review
        JOIN User on User.UserId = ?
        WHERE Review.UserIdx = User.UserIdx;";

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

![API Step](/assets/api/api25.JPG)

![API Step](/assets/api/api26.JPG)
<br>
<hr>
### &#127840; User 리뷰 목록 조회 API

```
$r->addRoute('GET', '/user/{user_id}/review', ['IndexController', 'getUserReview']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 14
* API Name : User 리뷰 목록 조회 API
* 마지막 수정 날짜 : 20.08.17
*/
case "getUserReview":
    http_response_code(200);

    $keyword = $_GET['userId'];

    if(!isValidId($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getUserReview($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;

```

IndexController.php에 관련 포맷 정의  

```
function getUserReview($keyword)
{
    $pdo = pdoSqlConnect();

    $query = "SELECT Review.Contents, Review.Tag, GROUP_CONCAT(ReviewPicture.Picture SEPARATOR '|') AS Picture,
            CASE
                WHEN TIMESTAMPDIFF(DAY, Review.Date, NOW()) < 1 THEN CONCAT('방금 전')
                WHEN TIMESTAMPDIFF(DAY, Review.Date, NOW()) <= 1 THEN CONCAT('어제')
                WHEN TIMESTAMPDIFF(DAY, Review.Date, NOW()) <= 7 THEN CONCAT('이번 주')
                WHEN TIMESTAMPDIFF(DAY, Review.Date, NOW()) <= 29 THEN CONCAT(CAST(TIMESTAMPDIFF(DAY, Review.Date, NOW()) / 7 as unsigned), '주 전')
                WHEN TIMESTAMPDIFF(MONTH , Review.Date, NOW()) <= 1 THEN CONCAT('지난 달')
                WHEN TIMESTAMPDIFF(MONTH , Review.Date, NOW()) < 12 THEN CONCAT(TIMESTAMPDIFF(MONTH , Review.Date, NOW()), '개월 전')
                ELSE CONCAT('작년')
            END AS Date
             , Review.Star, Store.Name, Store.StoreId
        FROM Review
        JOIN User on User.UserId = 'judy'
        JOIN  Store
        ON Review.UserIdx = User.UserIdx AND Review.isDeleted = 'N' AND Review.StoreIdx = Store.StoreIdx
        JOIN ReviewPicture
        ON Review.ReviewIdx = ReviewPicture.ReviewIdx
        GROUP BY Review.ReviewIdx, Review.Date
        ORDER BY Review.Date DESC;";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    for($i=0; $i<sizeof($res); $i++)
    {
        $res[$i] = array_filter($res[$i]);
    }

    return $res;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api27.JPG)

![API Step](/assets/api/api28.JPG)
<br>
<hr>
### &#127840; User 보유 쿠폰 조회 API

```
$r->addRoute('GET', '/user/{user_id}/coupon', ['IndexController', 'getUserCoupon']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 20
* API Name : User 보유 쿠폰 조회 API
* 마지막 수정 날짜 : 20.08.14
*/
case "getUserCoupon":
    http_response_code(200);

    $keyword = $vars['user_id'];

    if(!isValidId($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getUserCoupon($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 20 User 보유 쿠폰 조회
function getUserCoupon($keyword)
{
    $pdo = pdoSqlConnect();

    $query = "SELECT couponIdx, price, coupon, date_format(startDate,'%Y.%m.%d') AS startDate, date_format(endDate,'%Y.%m.%d') AS endDate
        FROM Coupon
        JOIN User ON User.userId = ? AND User.userIdx = Coupon.userIdx
        WHERE Coupon.isUsed = 'N';";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api35.JPG)

![API Step](/assets/api/api36.JPG)
<br>
<hr>
### &#127840; User 이번달 누적 주문 횟수 API

```
$r->addRoute('GET', '/user/{user_id}/order-cnt', ['IndexController', 'getUserOrderCount']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 21
* API Name : User 이번달 누적 주문 횟수 API
* 마지막 수정 날짜 : 20.08.20
*/
case "getUserOrderCount":
    http_response_code(200);

    $keyword = $vars['user_id'];

    if(!isValidId($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getUserOrderCount($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 21 User 이번달 누적 주문 횟수
function getUserOrderCount($keyword)
{
    $pdo = pdoSqlConnect();

    $query = "SELECT COUNT(*) AS orderCount, User.level
        FROM OrderMenu
        JOIN User ON User.userId = ? AND User.userIdx = OrderMenu.userIdx
        WHERE TIMESTAMPDIFF(MONTH , OrderMenu.date, NOW()) < 1
        GROUP BY User.userIdx;";

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

![API Step](/assets/api/api37.JPG)
<br>
<hr>
### &#127840; 해당 단어가 포함되는 가게 검색 API

```
$r->addRoute('GET', '/store', ['IndexController', 'getStoreWord']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 28
* API Name : 해당 단어가 포함되는 가게 검색 API
* 마지막 수정 날짜 : 20.08.20
*/
case "getStoreWord":
    http_response_code(200);

    $keyword = $_GET['word'];

    if($keyword == null){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "키워드가 입력되지 않았습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getStoreWord($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 28 가게 키워드 검색
function getStoreWord($keyword)
{
    $pdo = pdoSqlConnect();

    $query = "SELECT Store.name, Store.star, Store.min, Store.represent, Store.isOpen, Store.isOrder
        FROM Store
        WHERE Store.name LIKE CONCAT('%', ?, '%');";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api38.JPG)

![API Step](/assets/api/api39.JPG)
<br>
<hr>
### &#127840; 리뷰 상세 정보 조회 API

```
$r->addRoute('GET', '/user/review/{review_idx}', ['IndexController', 'getUserReviewDetail']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 29
* API Name : User 리뷰 목록 조회 API
* 마지막 수정 날짜 : 20.08.17
*/
case "getUserReviewDetail":
    http_response_code(200);

    $keyword = $vars['review_idx'];
    if(!isValidReviewIdx($keyword)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 리뷰 Idx입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $res->result = getUserReviewDetail($keyword);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 출력 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 29 User 리뷰 상세 조회
function getUserReviewDetail($keyword)
{
    $pdo = pdoSqlConnect();

    $query = "SELECT Review.contents, Review.tag, GROUP_CONCAT(ReviewPicture.picture SEPARATOR '|') AS picture,
                    CASE
                        WHEN TIMESTAMPDIFF(DAY, Review.date, NOW()) < 1 THEN CONCAT('방금 전')
                        WHEN TIMESTAMPDIFF(DAY, Review.date, NOW()) <= 1 THEN CONCAT('어제')
                        WHEN TIMESTAMPDIFF(DAY, Review.date, NOW()) <= 7 THEN CONCAT('이번 주')
                        WHEN TIMESTAMPDIFF(DAY, Review.date, NOW()) <= 29 THEN CONCAT(CAST(TIMESTAMPDIFF(DAY, Review.date, NOW()) / 7 as unsigned), '주 전')
                        WHEN TIMESTAMPDIFF(MONTH , Review.date, NOW()) <= 1 THEN CONCAT('지난 달')
                        WHEN TIMESTAMPDIFF(MONTH , Review.date, NOW()) < 12 THEN CONCAT(TIMESTAMPDIFF(MONTH , Review.date, NOW()), '개월 전')
                        ELSE CONCAT('작년')
                    END AS date
                     , Review.star, Store.name, Store.storeId, Review.reviewIdx
                FROM Review
                JOIN  Store
                ON Review.reviewIdx = ? AND Review.isDeleted = 'N' AND Review.storeIdx = Store.storeIdx
                JOIN ReviewPicture
                ON Review.reviewIdx = ReviewPicture.reviewIdx
                GROUP BY Review.reviewIdx, Review.date;";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    for($i=0; $i<sizeof($res); $i++)
    {
        $res[$i] = array_filter($res[$i]);
    }

    return $res;
}

```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api40.JPG)