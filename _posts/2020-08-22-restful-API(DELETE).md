---  
layout: post  
title: "배달 어플 API 구현 및 명세서 작성하기 - DELETE 방식 사용"  
subtitle: "배달 어플 API 구현 및 명세서 작성하기 - DELETE 방식 사용"  
categories: restful
tags: DB API PHP DELETE
comments: false  
---  

## API 구현 및 명세서 작성하기 - DELETE 방식

### &#127827; 회원 정보 삭제 API

```
$r->addRoute('DELETE', '/user/{user_id}', ['IndexController', 'deleteUser']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 31
* API Name : 회원 정보 삭제 API
* 마지막 수정 날짜 : 20.08.22
*/
case "deleteUser":
    http_response_code(200);

    $userId = $vars['user_id'];

    deleteUser($userId);

    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 삭제 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;

```

IndexController.php에 관련 포맷 정의  

```
// API NO. 31 회원 정보 삭제
function deleteUser($userId)
{
    $pdo = pdoSqlConnect();

    $query = "UPDATE User SET isDeleted = 'Y' WHERE userId = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$userId]);
    $st = null;
    $pdo = null;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api59.JPG)
<br>
<hr>
### &#127835; 가게 정보 삭제 API

```
$r->addRoute('DELETE', '/store/{store_id}', ['IndexController', 'deleteStore']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 32
* API Name : 가게 정보 삭제 API
* 마지막 수정 날짜 : 20.08.22
*/
case "deleteStore":
    http_response_code(200);

    $storeId = $vars['store_id'];

    deleteStore($storeId);

    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 삭제 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 32 가게 정보 삭제
function deleteStore($storeId)
{
    $pdo = pdoSqlConnect();

    $query = "UPDATE Store SET isDeleted = 'Y' WHERE storeId = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$storeId]);
    $st = null;
    $pdo = null;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api59.JPG)
<br>
<hr>
### &#127818; User 리뷰 삭제 API

```
$r->addRoute('DELETE', '/user/review/{review_idx}', ['IndexController', 'deleteUserReview']);
```

Index.php에 라우팅 정의  

```

```

IndexController.php에 관련 포맷 정의  

```
// API NO. 33 회원 리뷰 삭제
function deleteUserReview($review_idx)
{
    $pdo = pdoSqlConnect();

    $query = "UPDATE Review SET isDeleted = 'Y' WHERE reviewIdx = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$review_idx]);
    $st = null;
    $pdo = null;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api60.JPG)
<br>
<hr>
### &#127849; User 주문 내역 삭제 API

```
$r->addRoute('DELETE', '/user/order/{order_num}', ['IndexController', 'deleteUserOrder']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 34
* API Name : User 주문내역 삭제 API
* 마지막 수정 날짜 : 20.08.22
*/
case "deleteUserOrder":
    http_response_code(200);

    $order_num = $vars['order_num'];

    if(!isCheckOrderNum($order_num)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 Order Number 입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    deleteUserOrder($order_num);

    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 삭제 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 34 회원 주문내역 삭제
function deleteUserOrder($order_num)
{
    $pdo = pdoSqlConnect();

    $query = "UPDATE OrderMenu SET isDeleted = 'Y' WHERE orderNumber = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$order_num]);
    $st = null;
    $pdo = null;
}

// OrderNumber 유효성 체크
function isCheckOrderNum($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT EXISTS (SELECT * FROM OrderMenu WHERE orderNumber = ? AND isDeleted = 'N') AS exist;";

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

![API Step](/assets/api/api61.JPG)
<br>
<hr>
### &#127829; Store 메뉴 삭제 API

```
$r->addRoute('DELETE', '/store/menu/{menu_num}', ['IndexController', 'deleteStoreMenu']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 35
* API Name : Store 메뉴 삭제 API
* 마지막 수정 날짜 : 20.08.22
*/
case "deleteStoreMenu":
    http_response_code(200);

    $menu_num = $vars['menu_num'];

    if(!isValidMenuNum($menu_num)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 Menu Number 입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    deleteStoreMenu($menu_num);

    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 삭제 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 35 가게 메뉴 삭제
function deleteStoreMenu($menu_num)
{
    $pdo = pdoSqlConnect();

    $query = "UPDATE Menu SET isDeleted = 'Y' WHERE menuNum = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$menu_num]);
    $st = null;
    $pdo = null;
}

// Menu Number 유효성 체크
function isValidMenuNum($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT EXISTS (SELECT * FROM Menu WHERE menuNum = ? AND isDeleted = 'N') AS exist;";

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

![API Step](/assets/api/api62.JPG)
