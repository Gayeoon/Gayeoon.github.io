---  
layout: post  
title: "배달 어플 API 구현 및 명세서 작성하기 - POST 방식 사용"  
subtitle: "배달 어플 API 구현 및 명세서 작성하기 - POST 방식 사용"  
categories: restful
tags: DB API PHP POST
comments: false  
---  

## API 구현 및 명세서 작성하기 - POST 방식

### &#127847; User 회원가입 API

```
$r->addRoute('POST', '/user', ['IndexController', 'createUser']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 15
* API Name : 회원가입 API
* 마지막 수정 날짜 : 20.08.17
*/
case "createUser":
    http_response_code(200);

    if($req->UserId == null) {
        $res->isSuccess = FALSE;
        $res->code = 300;
        $res->message = "아이디 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if(idCheck($req->UserId)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "이미 존재하는 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->UserPw == null){
        $res->isSuccess = FALSE;
        $res->code = 400;
        $res->message = "비밀번호 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->Phone == null){
        $res->isSuccess = FALSE;
        $res->code = 500;
        $res->message = "사용자 번호 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->Email == null){
        $res->isSuccess = FALSE;
        $res->code = 600;
        $res->message = "이메일 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if ($req->Name == null){
        $Name = $req->UserId;
        createUser($req->UserId, $req->UserPw, $Name, $req->Phone, $req->Email, $req->MailReceiving, $req->SmsReceiving);
        $res->isSuccess = TRUE;
        $res->code = 100;
        $res->message = "테스트 성공";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    createUser($req->UserId, $req->UserPw, $req->Name, $req->Phone, $req->Email, $req->MailReceiving, $req->SmsReceiving);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "테스트 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function createUser($UserId, $UserPw, $Name, $Phone, $Email, $MailReceiving, $SmsReceiving){
    $pdo = pdoSqlConnect();
    $query = "INSERT INTO User (UserId, UserPw, Name, Phone, Email, Level, MailReceiving, SmsReceiving)
 VALUES (?,?,?,?,?,0,?,?);";

    $st = $pdo->prepare($query);
    $st->execute([$UserId, $UserPw, $Name, $Phone, $Email, $MailReceiving, $SmsReceiving]);

    $st = null;
    $pdo = null;
}


function idCheck($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT EXISTS (SELECT * FROM User WHERE UserId = ?) AS exist;";

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

![API Step](/assets/api/api29.JPG)

![API Step](/assets/api/api30.JPG)
<br>
<hr>
### &#127849; Store 회원가입 API

```
$r->addRoute('POST', '/store', ['IndexController', 'createStore']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 16
* API Name : Store 회원가입 API
* 마지막 수정 날짜 : 20.08.18
*/
case "createStore":
    http_response_code(200);
    if($req->StoreId == null) {
        $res->isSuccess = FALSE;
        $res->code = 300;
        $res->message = "아이디 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if(isValidStoreId($req->StoreId)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "이미 존재하는 ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }
    if($req->StorePw == null){
        $res->isSuccess = FALSE;
        $res->code = 400;
        $res->message = "비밀번호 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->Phone == null){
        $res->isSuccess = FALSE;
        $res->code = 500;
        $res->message = "Store 번호 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->Type == null){
        $res->isSuccess = FALSE;
        $res->code = 600;
        $res->message = "Type 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->Name == null){
        $res->isSuccess = FALSE;
        $res->code = 700;
        $res->message = "Store 이름 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->Type == null){
        $res->isSuccess = FALSE;
        $res->code = 800;
        $res->message = "Category 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->OpenTime == null || $req->CloseTime == null){
        $res->isSuccess = FALSE;
        $res->code = 900;
        $res->message = "Open / Close 시간 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    createStore($req->StoreId, $req->StorePw, $req->Name, $req->Type, $req->Category,
        $req->OpenTime, $req->CloseTime, $req->IsDelivery, $req->IsOrder, $req->IsBmart,
        $req->Represent, $req->Min, $req->Tip, $req->Phone, $req->Explan, $req->DeliveryTime,
        $req->IsOpenList, $req->IsUltraCall, $req->Latitude, $req->Longitude);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "테스트 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;

```

IndexController.php에 관련 포맷 정의  

```
function createStore($StoreId, $StorePw, $Name, $Type, $Category,
                    $OpenTime, $CloseTime, $IsDelivery, $IsOrder, $IsBmart,
                    $Represent, $Min, $Tip, $Phone, $Explan, $DeliveryTime,
                    $IsOpenList, $IsUltraCall, $Latitude, $Longitude){
    $pdo = pdoSqlConnect();
    $query = "INSERT INTO Store (StoreId, StorePw, Name, Type, Category, IsDeleted,
                    OpenTime, CloseTime, IsOpen, IsDelivery, IsOrder, IsBmart,
                    Represent, Min, Tip, Phone, Explan, DeliveryTime,
                    IsOpenList, IsUltraCall, Latitude, Longitude)
 VALUES (?, ?, ?, ?, ?,'N',?, ?, 'Y', ?,?,?,?,?, ?,?,?,?,?,?,?,?);";

    $st = $pdo->prepare($query);
    $st->execute([$StoreId, $StorePw, $Name, $Type, $Category,
        $OpenTime, $CloseTime, $IsDelivery, $IsOrder, $IsBmart,
        $Represent, $Min, $Tip, $Phone, $Explan, $DeliveryTime,
        $IsOpenList, $IsUltraCall, $Latitude, $Longitude]);

    $st = null;
    $pdo = null;
}

```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api31.JPG)

![API Step](/assets/api/api32.JPG)
<br>
<hr>
### &#127854; Store 메뉴 추가 API

```
$r->addRoute('POST', '/store/menu', ['IndexController', 'createStoreMenu']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 17
* API Name : Store Menu 추가 API
* 마지막 수정 날짜 : 20.08.18
*/
case "createStoreMenu":
    http_response_code(200);
    if($req->StoreId == null) {
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "아이디 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $StoreIdx = getStoreId($req->StoreId);

    if(checkMenu($StoreIdx['StoreIdx'], $req->Name)){
        $res->isSuccess = FALSE;
        $res->code = 300;
        $res->message = "해당 가게에 동일한 메뉴가 있습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->Name == null) {
        $res->isSuccess = FALSE;
        $res->code = 400;
        $res->message = "메뉴 이름 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->Price == null) {
        $res->isSuccess = FALSE;
        $res->code = 500;
        $res->message = "가격 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    createStoreMenu($StoreIdx['StoreIdx'], $req->Name, $req->Picture, $req->Price, $req->MenuOption);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "테스트 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
function createStoreMenu($StoreIdx, $Name, $Picture, $Price, $MenuOption){
    $pdo = pdoSqlConnect();
    $query = "INSERT INTO Menu (StoreIdx, Name, Picture, Price, IsPossible, MenuOption)
 VALUES (?, ?, ?, ?, 'Y', ?);";

    $st = $pdo->prepare($query);
    $st->execute([$StoreIdx, $Name, $Picture, $Price, $MenuOption]);

    $st = null;
    $pdo = null;
}


function checkMenu($storeIdx, $name)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT EXISTS (SELECT * FROM Menu WHERE StoreIdx = ? AND NAME = ?) AS exist;";

    $st = $pdo->prepare($query);
    $st->execute([$storeIdx, $name]);
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

![API Step](/assets/api/api33.JPG)

![API Step](/assets/api/api34.JPG)
<br>
<hr>
### &#127822; User 리뷰쓰기 API

```
$r->addRoute('POST', '/user/review', ['IndexController', 'createReview']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 18
* API Name : User 리뷰쓰기 API
* 마지막 수정 날짜 : 20.08.20
*/
case "createReview":
    http_response_code(200);

    if($req->orderNum == null) {
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "주문 번호 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if(isValidOrderNum($req->orderNum)){
        $res->isSuccess = FALSE;
        $res->code = 300;
        $res->message = "이미 리뷰가 존재합니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $temp = getOrderNum($req->orderNum);
    $userIdx = $temp['userIdx'];
    $storeIdx = $temp['storeIdx'];


    if($req->contents == null) {
        $res->isSuccess = FALSE;
        $res->code = 400;
        $res->message = "contents 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    createReview($userIdx,  $storeIdx, $req->tag, $req->contents, $req->star, $req->orderNum);

    if($req->picture != null){
        $reviewIdx = getReviewIdx($req->orderNum);

        for($t=0; $t<sizeof($req->picture); $t++)
        {
            createReviewPicture($reviewIdx['reviewIdx'], $req->picture[$t]);
        }

    }

    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "테스트 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 18 리뷰 추가
function createReview($UserIdx, $StoreIdx, $Tag, $Contents, $Star, $OrderNumber){

    $pdo = pdoSqlConnect();
    $query = "INSERT INTO Review (userIdx, storeIdx, tag, contents, isDeleted, star, comment, commentIdx, orderNumber)
 VALUES (?, ?, ?, ?, 'N', ?, null, null, ?);";

    $st = $pdo->prepare($query);
    $st->execute([$UserIdx, $StoreIdx, $Tag, $Contents, $Star, $OrderNumber]);

    $st = null;
    $pdo = null;
}


// API NO. 18 리뷰 사진 추가
function createReviewPicture($ReviewIdx, $Picture){

    $pdo = pdoSqlConnect();
    $query = "INSERT INTO ReviewPicture (reviewIdx, picture)
 VALUES (?, ?);";

    $st = $pdo->prepare($query);
    $st->execute([$ReviewIdx, $Picture]);

    $st = null;
    $pdo = null;
}

// Review Idx 가져오기
function getReviewIdx($orderNum)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT reviewIdx
        FROM Review
        WHERE orderNumber = ?";

    $st = $pdo->prepare($query);
    $st->execute([$orderNum]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res[0];
}


// Review Idx 가져오기
function getOrderNum($orderNum)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT userIdx, storeIdx
        FROM OrderMenu
        WHERE orderNumber = ?";

    $st = $pdo->prepare($query);
    $st->execute([$orderNum]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return $res[0];
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api41.JPG)

![API Step](/assets/api/api42.JPG)
<br>
<hr>
### &#127821; User 쿠폰발급 API

```
$r->addRoute('POST', '/user/coupon', ['IndexController', 'createCoupon']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 19
* API Name : User 쿠폰발급 API
* 마지막 수정 날짜 : 20.08.20
*/
case "createCoupon":
    http_response_code(200);

    if($req->userId == null) {
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "아이디 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->price == null){
        $res->isSuccess = FALSE;
        $res->code = 300;
        $res->message = "가격 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->coupon == null){
        $res->isSuccess = FALSE;
        $res->code = 400;
        $res->message = "쿠폰 내용 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $userIdx = getUserId($req->userId);

    if($req->storeId ==  null){
        createCoupon($userIdx['userIdx'], $req->price, $req->coupon, $req->endDate, 0);
    }else{
        $storeIdx = getStoreId($req->storeId);
        createCoupon($userIdx['userIdx'], $req->price, $req->coupon, $req->endDate, $storeIdx['storeIdx']);
    }

    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "테스트 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 19 쿠폰 발급
function createCoupon($userIdx, $price, $coupon, $endDate, $storeIdx){
    $pdo = pdoSqlConnect();
    if($storeIdx == 0){
        $query = "INSERT INTO Coupon (userIdx, price, coupon, isUsed, endDate, storeIdx)
        VALUES (?, ?, ?, 'N', ?, 0);";

        $st = $pdo->prepare($query);
        $st->execute([$userIdx, $price, $coupon, $endDate]);
    }else {
        $query = "INSERT INTO Coupon (userIdx, price, coupon, isUsed, endDate, storeIdx)
        VALUES (?, ?, ?, 'N', ?, ?);";

        $st = $pdo->prepare($query);
        $st->execute([$userIdx, $price, $coupon, $endDate, $storeIdx]);
    }
    $st = null;
    $pdo = null;
}

```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api43.JPG)

![API Step](/assets/api/api44.JPG)
<br>
<hr>
### &#127849; User 음식 주문 API

```
$r->addRoute('POST', '/user/order', ['IndexController', 'createOrder']);
```

Index.php에 라우팅 정의  

```
/*
       * API No. 22
       * API Name : User 음식 주문 API
       * 마지막 수정 날짜 : 20.08.20
       */
case "createOrder":
    http_response_code(200);

    if($req->userId == null) {
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "User 아이디 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req->storeId == null) {
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "Store 아이디 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $userIdx = getUserId($req->userId);
    $storeIdx = getStoreId($req->storeId);

    $toStoreMemo = $req->toStoreMemo;
    if($req->toStoreMemo == null)
        $toStoreMemo = "(없음)";

    createOrder($userIdx['userIdx'],  $storeIdx['storeIdx'], $req->address, $req->number, $toStoreMemo, $req->toRiderMemo, $req->payment, $req->type);

    $orderNum = findOrderNum();

     for($t=0; $t<sizeof($req->menuNum); $t++)
        {
            createOrderList($orderNum, $req->menuNum[$t], $req->menuCnt[$t], $req->menuOption[$t]);
        }


    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "테스트 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 22 User 음식주문
function createOrder($userIdx, $storeIdx, $address, $number, $toStoreMemo, $toRiderMemo, $payment, $type)
{
    $pdo = pdoSqlConnect();

    $query = "INSERT INTO OrderMenu(userIdx, storeIdx, address, number, toStoreMemo, toRiderMemo, payment, isDelivered, isReview, type, isDeleted)
        VALUES (?,?,?,?,?,?,?,'N','Y',?,'N');";

    $st = $pdo->prepare($query);
    $st->execute([$userIdx, $storeIdx, $address, $number, $toStoreMemo, $toRiderMemo, $payment, $type]);

    $st = null;
    $pdo = null;
}

// API NO. 22 User 음식주문 - 메뉴 추가
function createOrderList($orderNum, $menuNum, $menuCnt, $menuOption)
{
    $pdo = pdoSqlConnect();

    $query = "INSERT INTO OrderMenuList(orderNumber, menuNum, menuCnt, menuOption)
        VALUES (?, ?, ?, ?);";

    $st = $pdo->prepare($query);
    $st->execute([$orderNum, $menuNum, $menuCnt, $menuOption]);

    $st = null;
    $pdo = null;

}

// OrderNumber 인덱스 찾기
function findOrderNum()
{
    $pdo = pdoSqlConnect();
    $query = "SELECT MAX(orderNumber) AS orderNumber From OrderMenu";

    $st = $pdo->prepare($query);
    $st->execute([]);
    //    $st->execute();
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;
    //echo json_encode($res);
    return intval($res[0]['orderNumber']);
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api45.JPG)

![API Step](/assets/api/api46.JPG)