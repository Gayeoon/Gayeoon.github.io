---
layout: post
title:  "JPA"
subtitle:   "JPA"
categories: spring
tags: JPA SQL
comments: false
---
## JPA

### &#128204; SQL 중심적인 개발의 문제점
- CRUD 무한반복..&#128561;  
- SQL에 의존적인 개발을 피하기 어렵다.  
- 관계형 데이터베이스와 객체의 페러다임 불일치  
	&#10140; 관계형 데이터베이스는 데이터를 보관하는게 목표 객체는 캡슐화가 목표!  
- 처음 실행하는 SQL에 따라 탐색 범위 결정  
	&#10140; 엔티티 신뢰 문제가 발생한다!  
- 모든 객체를 미리 로딩할 수 없다.  
	&#10140; 상황에 따라 동일한 조회 메소드 여러번 생성  
- 객체답게 모델링 할수록 매핑 작업만 늘어난다.  

<br>
<hr>
### &#128204; 객체와 관계형 데이터베이스의 차이
- 상속  
- 연관관계  
- 데이터 타입  
- 데이터 식별 방법  

#### 상속
![spring img](/assets/spring/24.JPG) 

- Album 저장  
	1. 객체 분해  
	2. INSERT INTO ITEM ...  
	3. INSERT INTO ALBUM …  
&#10140; 두번 저장해야 한다!!	 
> ❓ Album을 자바 컬렉션에 저장한다면?  
> `list.add(album);` 코드 한줄로 끝!!  

- Album 조회
	1. 각각의 테이블에 따른 조인 SQL 작성  
	2. 각각의 객체 생성  
	3. 기타 등등...&#128565;  
	4. 그래서 **DB에 저장할 객체에는 상속 관계 안쓴다!**  
> ❓ Album을 자바 컬렉션에 저장한다면?  
> `Album album = list.get(albumId);`   
> `Item item = list.get(albumId);` 부모 타입 조회해서 다형성 활용할 수도 있음!!  

#### 연관관계
![spring img](/assets/spring/24.JPG)  
- 객체  
	+ 참조 사용  
	+ member.getTeam()  
	+ Member &#10140; Team은 가능하지만 Team &#10140; Member는 불가능!! **(단방향)**  
- 테이블  
	+ 외래 키 사용  
	+ JOIN ON M.TEAM_ID = T.TEAM_ID  
	+ Member &#10140; Team AND Team &#10140; Member 둘 다 자유롭게 가능!! **(양방향)**  

#### 비교하기

**SQL 조회**  
{% highlight java %}
String memberId = "100";
Member member1 = memberDAO.getMember(memberId);
Member member2 = memberDAO.getMember(memberId);

member1 == member2; //다르다.
{% endhighlight %}

> ❓ 왜 다를까!?  
> {% highlight java %}
> class MemberDAO {
>
>  public Member getMember(String memberId) {
>		String sql = "SELECT * FROM MEMBER WHERE MEMBER_ID = ?";
>		...
>		
>		//JDBC API, SQL 실행
>		return new Member(...);
>	}
>}
>{% endhighlight %}

**자바 컬렉션에서 조회**
{% highlight java %}
String memberId = "100";
Member member1 = list.get(memberId);
Member member2 = list.get(memberId);

member1 == member2; //같다.
{% endhighlight %}
