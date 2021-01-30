---  
layout: post  
title: "배달 어플 API 구현 및 명세서 작성하기 - PATCH 방식 사용"  
subtitle: "배달 어플 API 구현 및 명세서 작성하기 - PATCH 방식 사용"  
categories: restful
tags: DB API PHP PATCH
comments: false  
---  

## API 구현 및 명세서 작성하기 - PATCH 방식

### &#127850; User 가게 찜 / 찜 취소 API
```
$r->addRoute('PATCH', '/user/{user_id}/choose', ['IndexController', 'editChoose']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 23
* API Name : User 가게 찜 / 찜 취소 API
* 마지막 수정 날짜 : 20.08.21
*/
case "editChoose":
    http_response_code(200);

    $keyword = $vars['user_id'];

    if(!isValidStoreId($req -> storeId)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 Store ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $userIdx = getUserId($keyword)['userIdx'];
    $storeIdx = getStoreId($req->storeId)['storeIdx'];

    $ans = isChoosed($userIdx, $storeIdx);

    if($req -> status == 0 && $ans == 0){
        $res->isSuccess = FALSE;
        $res->code = 300;
        $res->message = "찜이 되어있지 않는 Store ID입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req -> status == 0 && $ans == 1){
        editChoose($userIdx, $storeIdx, 'Y');
        $res->isSuccess = TRUE;
        $res->code = 100;
        $res->message = "정보 수정 성공";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req -> status == 1 && $ans == 1){
        editChoose($userIdx, $storeIdx, 'N');
        $res->isSuccess = TRUE;
        $res->code = 100;
        $res->message = "정보 수정 성공";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req -> status == 1 && $ans == 0){
        createChoose($userIdx, $storeIdx, 'N');
        $res->isSuccess = TRUE;
        $res->code = 100;
        $res->message = "정보 수정 성공";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    break;
```

IndexController.php에 관련 포맷 정의  

```
// choose 테이블에 있는지 확인
function isChoosed($userIdx, $storeIdx)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT EXISTS (SELECT * FROM Choose WHERE userIdx = ? AND storeIdx = ?) AS exist;";

    $st = $pdo->prepare($query);
    $st->execute([$userIdx, $storeIdx]);
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;

    return intval($res[0]['exist']);
}

// API NO. 23 User 가게 찜 / 취소
function editChoose($userIdx, $storeIdx, $isDeleted)
{
    $pdo = pdoSqlConnect();

    $query = "UPDATE Choose SET isDeleted = ?
        WHERE userIdx = ? AND storeIdx = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$isDeleted, $userIdx, $storeIdx]);

    $st = null;
    $pdo = null;

}

// API NO. 23 User 가게 찜 생성
function createChoose($userIdx, $storeIdx, $isDeleted)
{
    $pdo = pdoSqlConnect();

    $query = "INSERT INTO Choose(userIdx, storeIdx, isDeleted)
        VALUES (?, ?, ?);";

    $st = $pdo->prepare($query);
    $st->execute([$userIdx, $storeIdx, $isDeleted]);

    $st = null;
    $pdo = null;

}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api47.JPG)

![API Step](/assets/api/api48.JPG)
<br>
<hr>
### &#127838; User 리뷰 수정 API

```
$r->addRoute('PATCH', '/user/review/{review_idx}', ['IndexController', 'editUserReview']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 24
* API Name : User 리뷰 수정 API
* 마지막 수정 날짜 : 20.08.21
*/
case "editUserReview":
    http_response_code(200);

    $reviewIdx = $vars['review_idx'];

    if(!isValidReviewIdx($reviewIdx)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 Review Idx입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    if($req -> contents != null){
        editUserReview($reviewIdx, $req -> contents, 0);
    }

    if($req -> star != null){
        editUserReview($reviewIdx, $req -> star, 1);
    }

    if($req->picture != null){
        deleteReviewPicture($reviewIdx);

        for($t=0; $t<sizeof($req->picture); $t++)
        {
            createReviewPicture($reviewIdx, $req->picture[$t]);
        }
    }

    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 수정 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 24 User 리뷰 수정
function editUserReview($reviewIdx, $temp, $flag)
{
    $pdo = pdoSqlConnect();

    // flag = 0 : contents 수정
    // flag = 1 : star 수정
    if($flag == 0){
        $query = "UPDATE Review SET contents = ?
        WHERE reviewIdx = ?;";

        $st = $pdo->prepare($query);
        $st->execute([$temp, $reviewIdx]);
    }

    else if($flag == 1){
        $query = "UPDATE Review SET star = ?
        WHERE reviewIdx = ?;";

        $st = $pdo->prepare($query);
        $st->execute([$temp, $reviewIdx]);
    }

    $st = null;
    $pdo = null;

}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api49.JPG)

![API Step](/assets/api/api50.JPG)
<br>
<hr>
### &#127830; User 쿠폰 사용  API

```
$r->addRoute('PATCH', '/user/coupon/{coupon_idx}', ['IndexController', 'useCoupon']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 28
* API Name : User 쿠폰 사용 API
* 마지막 수정 날짜 : 20.08.21
*/
case "useCoupon":
    http_response_code(200);

    $couponIdx = $vars['coupon_idx'];

    if(!isValidCouponIdx($couponIdx)){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "유효하지 않은 쿠폰입니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $ans = getCouponStore($couponIdx);
    $storeIdx = getStoreId($req->storeId)['storeIdx'];

    if($ans != 0 && $ans != $storeIdx){
        $res->isSuccess = FALSE;
        $res->code = 300;
        $res->message = "해당 가게에서는 쿠폰을 사용하실 수 없습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }


    useCoupon($couponIdx);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 수정 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// CouponIdx 유효성 체크
function isValidCouponIdx($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT EXISTS (SELECT * FROM Coupon WHERE couponIdx = ? AND isUsed = 'N' AND TIMESTAMPDIFF(DAY, endDate, NOW()) < 1) AS exist;";

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


// CouponIdx 사용 가능 가게 유효성 체크
function getCouponStore($keyword)
{
    $pdo = pdoSqlConnect();
    $query = "SELECT storeIdx FROM Coupon WHERE couponIdx = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$keyword]);
    //    $st->execute();
    $st->setFetchMode(PDO::FETCH_ASSOC);
    $res = $st->fetchAll();

    $st = null;
    $pdo = null;
    //echo json_encode($res);
    return intval($res[0]['storeIdx']);
}

// API NO. 28 User 쿠폰 사용
function useCoupon($couponIdx)
{
    $pdo = pdoSqlConnect();

    $query = "UPDATE Coupon SET isUsed = 'Y' WHERE couponIdx = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$couponIdx]);
    $st = null;
    $pdo = null;

}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api51.JPG)

![API Step](/assets/api/api52.JPG)
<br>
<hr>
### &#127819; Store 오픈 상태 변경 (가게 휴가) API

```
 $r->addRoute('PATCH', '/store/{store_id}/is-open', ['IndexController', 'editStoreOpen']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 25
* API Name : Store 오픈 상태 변경 API
* 마지막 수정 날짜 : 20.08.21
*/
case "editStoreOpen":
    http_response_code(200);

    $storeId = $vars['store_id'];
    $storeIdx = getStoreId($storeId)['storeIdx'];

    if($req -> isOpen != 0 && $req -> isOpen == null){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "Open 상태가 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $temp = 'N';
    if($req -> isOpen == 0){
        $temp = 'Y';
    }

    editStoreOpen($storeIdx, $temp);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 수정 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 25 Store 오픈 상태 변경
function editStoreOpen($storeIdx, $flag)
{
    $pdo = pdoSqlConnect();

    $query = "UPDATE Store SET isOpen = ? WHERE storeIdx = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$flag, $storeIdx]);
    $st = null;
    $pdo = null;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api53.JPG)

![API Step](/assets/api/api54.JPG)
<br>
<hr>
### &#127853; Store 메뉴 상태 변경 (품절) API

```
$r->addRoute('PATCH', '/store/{menu_num}/menu/possible', ['IndexController', 'editMenuPossible']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 26
* API Name : Store 메뉴 상태 변경 API
* 마지막 수정 날짜 : 20.08.21
*/
case "editMenuPossible":
    http_response_code(200);

    $menuNum = $vars['menu_num'];

    if($req -> isPossible != 0 && $req -> isPossible == null){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "Possible 상태가 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    $temp = 'N';
    if($req -> isPossible == 0){
        $temp = 'Y';
    }

    editMenuPossible($menuNum, $temp);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 수정 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 26 Store 메뉴 상태 변경
function editMenuPossible($menuNum, $flag)
{
    $pdo = pdoSqlConnect();

    $query = "UPDATE Menu SET isPossible = ? WHERE menuNum = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$flag, $menuNum]);
    $st = null;
    $pdo = null;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api55.JPG)

![API Step](/assets/api/api56.JPG)
<br>
<hr>
### &#127851; Store 메뉴 상태 변경 (메뉴 이미지) API

```
$r->addRoute('PATCH', '/store/{menu_num}/menu/picture', ['IndexController', 'editMenuPicture']);
```

Index.php에 라우팅 정의  

```
/*
* API No. 27
* API Name : Store 메뉴 상태 변경 API (이미지)
* 마지막 수정 날짜 : 20.08.21
*/
case "editMenuPicture":
    http_response_code(200);

    $menuNum = $vars['menu_num'];

    if($req -> picture == null){
        $res->isSuccess = FALSE;
        $res->code = 200;
        $res->message = "Picture 값이 누락되었습니다.";
        echo json_encode($res, JSON_NUMERIC_CHECK);
        break;
    }

    editMenuPicture($menuNum, $req->picture);
    $res->isSuccess = TRUE;
    $res->code = 100;
    $res->message = "정보 수정 성공";
    echo json_encode($res, JSON_NUMERIC_CHECK);
    break;
```

IndexController.php에 관련 포맷 정의  

```
// API NO. 27 Store 메뉴 상태 변경(이미지)
function editMenuPicture($menuNum, $image)
{
    $pdo = pdoSqlConnect();

    $query = "UPDATE Menu SET picture = ? WHERE menuNum = ?;";

    $st = $pdo->prepare($query);
    $st->execute([$image, $menuNum]);
    $st = null;
    $pdo = null;
}
```

IndexPdo.php에 기능 구현  

![API Step](/assets/api/api57.JPG)

![API Step](/assets/api/api58.JPG)
