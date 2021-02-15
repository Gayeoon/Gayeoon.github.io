---
layout: post
title:  "Web Application 환경 설정 및 도메인 분석 설계"
subtitle:   "Hello SHOP Project 환경 설정 및 도메인 분석 설계"
categories: spring
tags: JPA Spring Thymeleaf Hibernate Tomcat
comments: false
---
## Web Application 환경 설정

### &#128204; 프로젝트 생성
- 스프링 부트 스타터(https://start.spring.io/)  
- Project : Gradle Project  
- Spring Boot version : 2.4.2  
- Java version : 11  
- Group Id : jpabook  
- Artifact Id : jpashop  
- 사용 Dependencies : web, thymeleaf, jpa, h2, lombok, validation  

> ❓ Thymeleaf 사용 이유
> `<th th:text="${msgs.headers.name}"> name </th>` 다음과 같이 마크업을 깨지 않고 사용할 수 있습니다.  
> 또 웹 브라우저에서도 열어볼 수 있습니다!  

#### lombok 적용
![spring img](/assets/spring/19.JPG)  
File &#10140; Settings &#10140; Build &#10140; Compiler &#10140; Annotation Processors &#10140; Enable annotation processing 체크!  

[https://github.com/Gayeoon/Web-Application.git](https://github.com/Gayeoon/Web-Application.git)  

<br>
<hr>
### &#128204; H2 데이터베이스
[https://www.h2database.com](https://www.h2database.com)  
- H2 데이터베이스 설치  
- `jdbc:h2:~/jpashop` (최초 한번)  
- `~/jpashop.mv.db` 파일 생성 확인  
- 이후 부터는 `jdbc:h2:tcp://localhost/~/jpashop` 접속  
- Application.yml 파일 생성 ~~코드는 밑에!~~  
	+ show_sql : System.out에 하이버네이트 실행 SQL을 남깁니다.  
	+ org.hibernate.SQL : logger를 통해 하이버네이트 실행 SQL을 남깁니다.  
	
#### 동작 확인

회원 엔티티  
{% highlight java %}
@Entity
@Getter @Setter
public class Member {
	@Id @GeneratedValue
	private Long id;
	private String username;
}
{% endhighlight %}

회원 리포지토리  
{% highlight java %}
@Repository
public class MemberRepository {
	@PersistenceContext
	EntityManager em;

	public Long save(Member member) {
		em.persist(member);
		return member.getId();
	}

	public Member find(Long id) {
		return em.find(Member.class, id);
	}
}
{% endhighlight %}

테스트  
{% highlight java %}
@RunWith(SpringRunner.class)
@SpringBootTest
public class MemberRepositoryTest {
	@Autowired MemberRepository memberRepository;
	@Test
	@Transactional
	@Rollback(false)
	public void testMember() {
		Member member = new Member();
		member.setUsername("memberA");
		Long savedId = memberRepository.save(member);
		Member findMember = memberRepository.find(savedId);
		Assertions.assertThat(findMember.getId()).isEqualTo(member.getId());

		Assertions.assertThat(findMember.getUsername()).isEqualTo(member.getUsername());
	}
}
{% endhighlight %}
&#128226; `@Transactional` 어노테이션이 있으면 테스트 이후 Rollback되기 때문에 H2 DB에 추가되는 것을 확인하기 위해 `@Rollback(false)` 어노테이션을 추가했습니다!  

<br/>
#### &#128161; ERROR &#128161;
![spring img](/assets/spring/35.JPG)  
믿을 수 없게도 에러가 발생했습니다. &#128544;  
H2 DB랑 연결을 못하고 있는 것 같으니 .yml 파일을 확인해봅시다..  

{% highlight java %}
spring: #띄어쓰기 없음
	datasource: #띄어쓰기 2칸
		url: jdbc:h2:tcp://localhost/~/jpashop #띄어쓰기 4칸
		username: sa #띄어쓰기 4칸
		password: #띄어쓰기 4칸
		driver-class-name: org.h2.Driver #띄어쓰기 4칸
		
	jpa: #띄어쓰기 2칸
		hibernate: #띄어쓰기 4칸
			ddl-auto: create #띄어쓰기 6칸
		properties: #띄어쓰기 4칸
			hibernate: #띄어쓰기 6칸
				show_sql: true #띄어쓰기 8칸
				format_sql: true #띄어쓰기 8칸
				
logging.level: #띄어쓰기 없음
	org.hibernate.SQL: debug #띄어쓰기 2칸
{% endhighlight %}
application.yml 같은 .yml 파일은 띄어쓰기 2칸으로 계층을 만든다고 합니다!  
다음과 같이 띄어쓰기가 잘 되었는지 확인한 뒤에 다시 동작을 확인해보면 성공!!&#128582;  

<br>
<hr>
### &#128204; 도메인 모델과 테이블 설계
![spring img](/assets/spring/36.JPG)  
**회원, 주문, 상품의 관계**
- 회원은 여러 상품을 주문할 수 있습니다.  
- 또한 한번 주문할 때 여러 상품을 선택할 수 있으므로 주문과 상품은 **다대다 관계**입니다. 
하지만 이런 다대다 관계는 관계형 데이터베이스는 물론이고 엔티티에서도 거의 사용하지 않습니다. 
따라서 그림처럼 주문상품이라는 엔티티를 추가해서 다대다 관계를 **일대다, 다대일 관계**로 풀어냈습니다.  

**상품 분류**
- 상품은 도서, 음반, 영화로 구분되는데 상품이라는 공통 속성을 사용하므로 상속 구조로 표현했습니다.  

<br>
<hr>
### &#128204; 회원 엔티티 분석
![spring img](/assets/spring/37.JPG)  
- 회원(Member)  
	+ 이름과 임베디드 타입인 **주소**( Address ), 그리고 **주문**( orders ) 리스트를 가집니다.  
- 주문(Order)  
	+ 한 번 주문시 여러 상품을 주문할 수 있으므로 주문과 주문상품( OrderItem )은 일대다 관계입니다.  
	+ 주문은 상품을 주문한 **회원**과 **배송 정보**, **주문 날짜**, **주문 상태**( status )를 가지고 있습니다.  
	+ 주문 상태는 열거형을 사용했는데 **주문**( ORDER ), **취소**( CANCEL )을 표현할 수 있습니다.  
- 주문상품(OrderItem)  
	+ 주문한 **상품 정보**와 **주문 금액**( orderPrice ), **주문 수량**( count ) 정보를 가지고 있습니다.  
- 상품(Item)  
	+ **이름, 가격, 재고수량**( stockQuantity )을 가지고 있습니다.  
	+ 상품을 주문하면 재고수량이 줄어듭니다.  
	+ 상품의 종류로는 **도서, 음반, 영화**가 있는데 각각은 사용하는 속성이 조금씩 다릅니다.  
- 배송(Delivery)  
	+ 주문시 하나의 배송 정보를 생성합니다.  
	+ 주문과 배송은 일대일 관계입니다.  
- 카테고리(Category)  
	+ 상품과 다대다 관계를 맺습니다.  
	+ parent , child 로 **부모, 자식 카테고리를 연결**합니다.  
- 주소(Address)  
	+ 값 타입(임베디드 타입)입니다.  
	+ 회원과 배송(Delivery)에서 사용합니다.  
	
<br>
<hr>
### &#128204; 회원 테이블 분석
![spring img](/assets/spring/38.JPG)  

&#128226; 실제 코드에서는 DB에 소문자 + _(언더스코어) 스타일을 사용합니다!  

