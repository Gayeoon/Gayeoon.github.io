---  
layout: post  
title: "배달 어플 DataBase Query 작성하기"  
subtitle: "배달 어플 DataBase Query 작성하기"  
categories: restful
tags: DB ERD Query
comments: false  
---  

## DataBase ERD 설계 및 쿼리 작성 - 2

### &#128204; 배달의민족 Query 작성하기

#### &#127849; Point 화면

```
-- Point 페이지 포인트 이용 내역
SELECT Point, date_format(Date,'%Y-%m-%d') AS DATE , EndDate, Point.IsDeleted As Used, Store
FROM Point
JOIN User ON User.UserId = 'judy' AND Point.UserIdx = User.UserIdx
ORDER BY Date DESC;
```

![DB](/assets/aws/query1.png)

```
-- Point 페이지 보유 포인트 합계
SELECT sum(Point) AS Sum FROM Point
JOIN User ON UserId = 'judy'
WHERE User.UserIdx = Point.UserIdx AND Point.isDeleted <= 0
GROUP BY UserID;
```

<br>
![DB](/assets/aws/dbresult1.png)

<br>
<hr>
#### &#127844; My배민 화면

```
-- My 배민 페이지
SELECT User.Name, User.Level, COUNT(*) AS Coupon
FROM User
JOIN Coupon
ON User.UserIdx= Coupon.UserIdx AND Coupon.isUsed = 'N'
WHERE UserId = 'judy' AND isDeleted = 'N'
GROUP BY User.UserIdx;
```

<br>
![DB](/assets/aws/dbresult2.png)

<br>
<hr>
#### &#127850; 찜한가게 화면

```
-- 찜한가게 페이지
SELECT Store.Name, Store.Star, Store.Min, Store.Represent, Store.isOpen, Store.isOrder
FROM Choose
JOIN User on User.UserId = 'judy'
JOIN  Store
ON Choose.UserIdx = User.UserIdx AND Choose.StoreIdx = Store.StoreIdx;
```

![DB](/assets/aws/query4.png)
<br>
```
-- 찜한가게 페이지 바로결제 리스트
SELECT Store.Name, Store.Star, Store.Min, Store.Represent, Store.isOpen, Store.isOrder
FROM OrderMenu
JOIN User on User.UserId = 'judy'
JOIN  Store
ON OrderMenu.UserIdx = User.UserIdx AND OrderMenu.Payment = 0 AND OrderMenu.StoreIdx = Store.StoreIdx;
```

![DB](/assets/aws/query5.JPG)
<br>
```
-- 찜한가게 페이지 전화주문 리스트
SELECT Store.Name, Store.Star, Store.Min, Store.Represent, Store.isOpen, Store.isOrder
FROM OrderMenu
JOIN User on User.UserId = 'judy'
JOIN  Store
ON OrderMenu.UserIdx = User.UserIdx AND OrderMenu.Payment = 1 AND OrderMenu.StoreIdx = Store.StoreIdx;
```

![DB](/assets/aws/query6.JPG)
<br>
![DB](/assets/aws/dbresult3.png)

<br>
<hr>
#### &#127789; Store 화면

```
-- Store 상세 페이지 가게 정보
SELECT Store.Name, Star, Min,  Type, CONCAT(FORMAT(Store.Tip , 0), '원') AS Tip, Store.Phone, Store.DeliveryTime, Explan
FROM Store
WHERE Store.StoreID = 'mmmm';
```

![DB](/assets/aws/query7.JPG)
<br>
```
-- Store 상세 페이지 메뉴 목록
SELECT Menu.Name AS MenuName, CONCAT(FORMAT(Menu.Price , 0), '원') AS Price, Menu.Picture, Menu.isPossible, Menu.MenuNum
FROM Menu
JOIN  Store
ON Store.StoreID = 'mmmm' AND Menu.StoreIdx = Store.StoreIdx;
```

![DB](/assets/aws/query17.JPG)
<br>
```
-- Store 상세 페이지 가게 찜 Count
SELECT COUNT(*) As Choose
FROM Store
JOIN Choose
ON Store.StoreID = 'mmmm' AND Choose.StoreIdx = Store.StoreIdx;
```
<br>
```
-- Store 상세 페이지 리뷰 Count
SELECT COUNT(*) AS Review, COUNT(Review.Comment) AS Comments
FROM Store
JOIN Review
ON Store.StoreID = 'mmmm' AND Review.StoreIdx = Store.StoreIdx;
```

![DB](/assets/aws/query9.JPG)
<br>
![DB](/assets/aws/dbresult4.png)

<br>
<hr>
#### &#127848; 주문내역 화면

```
-- 주문 내역 화면 (배달 및 배민 오더 사용 주문)
SELECT OrderMenu.Type,
    CASE
        WHEN TIMESTAMPDIFF(DAY, OrderMenu.Date, NOW()) < 1 THEN CONCAT('오늘')
        WHEN TIMESTAMPDIFF(DAY, OrderMenu.Date, NOW()) <= 7 THEN CONCAT(TIMESTAMPDIFF(DAY, OrderMenu.Date, NOW()), '일 전')
        ELSE CONCAT(date_format(OrderMenu.Date,'%m/%d'), '(', SUBSTR( _UTF8'일월화수목금토', DAYOFWEEK(OrderMenu.Date), 1),')')
END AS Date,
Store.Category, Store.Name, OrderMenu.isReview, CONCAT(FORMAT(Store.Tip , 0), '원') AS Tip,
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
ORDER BY OrderMenu.Date DESC ;
```
![DB](/assets/aws/query14.JPG)

<br>
```
-- 주문 내역 화면 -> 주문한 메뉴 이름 및 가격
SELECT Menu.Name, Menu.Price
FROM OrderMenu
JOIN User on User.UserId = 'judy'
JOIN  OrderMenuList
ON OrderMenu.UserIdx = User.UserIdx AND OrderMenu.OrderNumber = OrderMenuList.OrderNumber
JOIN  Menu
ON Menu.MenuNum = OrderMenuList.MenuNum;

```

![DB](/assets/aws/query11.JPG)
<br>
```
-- 상세 주문 내역 화면
SELECT
       CASE
           WHEN HOUR(OrderMenu.Date) < 12 THEN date_format(OrderMenu.Date,'%Y년 %m월 %d일 오전 %h:%i')
           ELSE date_format(OrderMenu.Date,'%Y년 %m월 %d일 오후 %h:%i')
       END AS Date,
OrderMenu.OrderNumber,  Store.Name, CONCAT(FORMAT(Store.Tip , 0), '원') AS Tip, OrderMenu.isDelivered, Store.Phone, OrderMenu.Payment, OrderMenu.Address, Store.StoreId
FROM OrderMenu
JOIN  Store
ON OrderMenu.OrderNumber = 'B0QE01AX5S' AND OrderMenu.StoreIdx = Store.StoreIdx;
```

![DB](/assets/aws/query13.JPG)
<br>
```
-- 상세 주문 메뉴 내역
SELECT Menu.Name, CONCAT(FORMAT(Menu.Price , 0), '원') AS MenuPrice, Menu.MenuOption, OrderMenuList.MenuCnt
FROM OrderMenu
JOIN User on User.UserId = 'judy'
JOIN  OrderMenuList
ON OrderMenu.UserIdx = User.UserIdx AND OrderMenu.OrderNumber = 'B0QE01AX5S' AND OrderMenuList.OrderNumber = OrderMenu.OrderNumber
JOIN  Menu
ON Menu.MenuNum = OrderMenuList.MenuNum;
```

![DB](/assets/aws/query12.JPG)
<br>
<br>
![DB](/assets/aws/dbresult5.png)
<br>
![DB](/assets/aws/dbresult7.png)

<br>
<hr>
#### &#127856; Review 화면

```
-- 리뷰관리 페이지
SELECT Review.Contents, Review.Tag, GROUP_CONCAT(ReviewPicture.Picture SEPARATOR '|') AS Picture,
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
ORDER BY Review.Date DESC;
```

![DB](/assets/aws/query15.JPG)
<br>
```
-- 총 리뷰 수
SELECT COUNT(*) AS MyReview
FROM Review
JOIN User on User.UserId = 'judy'
WHERE Review.UserIdx = User.UserIdx;
```

<br>
![DB](/assets/aws/dbresult6.png)
