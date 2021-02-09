---
layout: post
title:  "Spring 입문"
subtitle:   "Spring 입문"
categories: spring
tags: Spring MVC Jdbc JPA AOP
comments: false
---
## Spring 입문

### &#128204; Spring Boot
스프링 부트 스타터 사이트에서 스프링 프로젝트 생성  
[https://start.spring.io/]   
- 프로젝트 선택  
	* Project: Gradle Project  
	* Spring Boot: 2.4.2  
	* Language: Java  
	* Packaging: Jar  
	* Java: 11  
- Project Metadata  
	* groupId: hello  
	* artifactId: hello-spring  
- Dependencies: Spring Web, Thymeleaf  

#### &#128226; TIP
IntelliJ 버전은 Gradle을 통해서 실행하는 것이 기본 설정이기 때문에 실행속도가 느립니다.  
Settings &#10140; Build, Execution, Deployment &#10140; Build Tools &#10140; Gradle  
Build and run using: Gradle &#10140; IntelliJ IDEA   
Run tests using: Gradle &#10140; IntelliJ IDEA  

![spring img](/assets/spring/1.JPG)

Settings는 `Ctrl + Alt + S` 를 누르면 열립니다!!  

### &#128204; 빌드하고 실행하기
콘솔로 이동  
1. ./gradlew build  
2. cd build/libs  
3. java -jar hello-spring-0.0.1-SNAPSHOT.jar  
4. 실행 확인  

<br>
<hr>
### &#128204; 정적 컨텐츠
![spring img](/assets/spring/2.JPG)  

### &#128204; MVC
MVC : Model, View, Controller  
![spring img](/assets/spring/3.JPG)  

### &#128204; API
&#10140; @ResponseBody 사용  
- HTTP의 BODY에 문자 내용을 직접 반환  
- viewResolver 대신에 HttpMessageConverter 가 동작  
- 기본 문자처리: StringHttpMessageConverter  
- 기본 객체처리: MappingJackson2HttpMessageConverter  
- byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음  

{% highlight java %}
@GetMapping("hello-api")
@ResponseBody
public Hello helloApi(@RequestParam("name") String name) {
	Hello hello = new Hello();
	hello.setName(name);
	return hello;
}
{% endhighlight %}

@ResponseBody 를 사용하고, 객체를 반환하면 객체가 JSON으로 변환됩니다.  

![spring img](/assets/spring/4.JPG) 

<br>
<hr>
### &#128204; 웹 어플리케이션 계층 구조
![spring img](/assets/spring/5.JPG)  
- 컨트롤러: 웹 MVC의 컨트롤러 역할  
- 서비스: 핵심 비즈니스 로직 구현  
- 리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리  
- 도메인: 비즈니스 도메인 객체, 예) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨  

### &#128204; 테스트
&#10140; 개발한 기능을 실행해서 테스트 할 때 자바의 main 메서드를 통해서 실행하거나, 웹 애플리케이션의
컨트롤러를 통해서 해당 기능을 실행합니다. 이러한 방법은 준비하고 실행하는데 오래 걸리고, 반복 실행하기
어렵고 여러 테스트를 한번에 실행하기 어렵다는 단점이 있습니다. 자바는 **JUnit이라는 프레임워크로 테스트를 실행**해서 이러한 문제를 해결합니다!  

&#128226; given, when, then 구조 사용해서 테스트 작성하기!  

- @AfterEach  
한번에 여러 테스트를 실행하면 메모리 DB에 직전 테스트의 결과가 남을 수 있습니다. 그럴 경우 이전 테스트 때문에 다음 테스트가 실패하는 상황이 벌어집니다.&#128543;  
@AfterEach 를 사용하면 각 테스트가 종료될 때 마다 이 기능을 실행하게 됩니다.  
이것을 이용해서 DB에 저장된 데이터를 삭제할 수 있습니다.&#128521;  

{% highlight java %}
@AfterEach
public void afterEach() {
	memberRepository.clearStore();
}
{% endhighlight %}

&#10024; 테스트는 각각 독립적으로 실행되어야 합니다! 테스트 순서에 의존관계가 있는 것은 좋은 테스트가 아닙니다!&#128581;  

- @BeforeEach  
각 테스트 실행 전에 호출됩니다.  
테스트가 서로 영향이 없도록 항상 새로운 객체를 생성하고,의존관계도 새로 맺어줍니다.  

{% highlight java %}
@BeforeEach
public void beforeEach() {
	memberRepository = new MemoryMemberRepository();
	memberService = new MemberService(memberRepository);
}
{% endhighlight %}

<br>
<hr>
### &#128204; Spring Been
- @Autowired  
생성자에 @Autowired 가 있으면 스프링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어줍니다.  
이렇게 객체 의존관계를 외부에서 넣어주는 것을 **DI (Dependency Injection), 의존성 주입**이라 합니다.  

- 스프링 빈을 등록하는 2가지 방법  
	- 컴포넌트 스캔과 자동 의존관계 설정  
	- 자바 코드로 직접 스프링 빈 등록하기  

### &#128204; 컴포넌트 스캔
- @Component 어노테이션이 있으면 스프링 빈으로 자동 등록됩니다.   
&#128226; @Controller 컨트롤러가 스프링 빈으로 자동 등록된 이유도 컴포넌트 스캔 때문입니다!!  
- @Component 를 포함하는 다음 어노테이션도 스프링 빈으로 자동 등록됩니다.  
	- @Controller  
	- @Service  
	- @Repository  
	
#### &#128226; TIP
스프링은 스프링 컨테이너에 스프링 빈을 등록할 때, 기본으로 **싱글톤**으로 등록합니다.(유일하게 하나만
등록해서 공유한다) 그렇기 때문에 같은 스프링 빈이면 모두 같은 인스턴스입니다!  

### &#128204; 자바 코드로 스프링 빈 등록

{% highlight java %}
@Configuration
public class SpringConfig {
	@Bean
	public MemberService memberService() {
		return new MemberService(memberRepository());
	}
	@Bean
	public MemberRepository memberRepository() {
		return new MemoryMemberRepository();
	}
}
{% endhighlight %}

- DI에는 필드 주입, setter 주입, 생성자 주입 이렇게 3가지 방법이 있습니다.  
- 하지만 의존관계가 실행중에 동적으로 변하는 경우는 거의 없으므로 생성자 주입을 권장한다고 합니당!  
- 실무에서는 주로 정형화된 컨트롤러, 서비스, 리포지토리 같은 코드는 컴포넌트 스캔을 사용합니다.  
그리고 정형화 되지 않거나, 상황에 따라 구현 클래스를 변경해야 하면 설정을 통해 스프링 빈으로 등록합니다.  

<br>
<hr>
### &#128204; H2 데이터베이스
1. H2 홈페이지에서 다운로드 받습니당! [https://www.h2database.com/]  
2. h2 &#10140; bin &#10140; h2.bat을 실행시킵니다.  
3. JDBC URL에 `jdbc:h2:~/test`를 넣고 연결한 뒤 `~/test.mv.db` 파일이 생성되었는지 확인합니다.  
4. 이후부터는 `jdbc:h2:tcp://localhost/~/test` 여기로 접속합니당  

<br>
#### &#128161; ERROR &#128161;
만약 오류가 뜬다면 웹 브라우저 주소를 `100.1.2.3` 에서 `localhost`로 변경하고 시도해봅니다.  
여전히 안된다면 컴퓨터를 종료하고 다시 시도해봅시다!!&#128549;  

~~JDBC는 생략하겠습니당&#128565;~~  

<br>
<hr>
### &#128204; JPA
- JPA는 기존의 반복 코드는 물론이고, 기본적인 SQL도 JPA가 직접 만들어서 실행해줍니다.  
- SQL과 데이터 중심의 설계에서 객체 중심의 설계로 패러다임을 전환을 할 수 있습니다.  
- 개발 생산성을 크게 높일 수 있습니다.  

{% highlight java %}
implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
runtimeOnly 'com.h2database:h2'
{% endhighlight %}

build.gradle 파일에 JPA, h2 데이터베이스 관련 라이브러리를 추가해줍니다.  

{% highlight java %}
import org.springframework.transaction.annotation.Transactional
@Transactional
public class MemberService {}
{% endhighlight %}

- 서비스 계층에 트랜잭션을 추가해줍니다.  
- 스프링은 해당 클래스의 메서드를 실행할 때 트랜잭션을 시작하고, 메서드가 정상 종료되면 트랜잭션을
커밋합니다. 만약 런타임 예외가 발생하면 롤백합니다.  
- **JPA를 통한 모든 데이터 변경은 트랜잭션 안에서 실행해야 합니다.**  

### &#128204; 스프링 데이터 JPA

{% highlight java %}
public interface SpringDataJpaMemberRepository extends JpaRepository<Member, Long>, MemberRepository {
	Optional<Member> findByName(String name);
}
{% endhighlight %}

&#10140; 스프링 데이터 JPA가 SpringDataJpaMemberRepository 를 스프링 빈으로 자동 등록해줍니다.  

![spring img](/assets/spring/6.JPG)   
- 인터페이스를 통한 기본적인 CRUD  
- findByName() , findByEmail() 처럼 메서드 이름 만으로 조회 기능 제공  
- 페이징 기능 자동 제공  

<br>
<hr>
### &#128204; AOP
- AOP가 필요한 상황  
	* 모든 메소드의 호출 시간을 측정하고 싶을때  
	* 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern)  
	
<br/>

{% highlight java %}
long start = System.currentTimeMillis();
try {
	
	...
	
} finally {
	long finish = System.currentTimeMillis();
	long timeMs = finish - start;
	System.out.println("join " + timeMs + "ms");
}
{% endhighlight %}

![spring img](/assets/spring/7.JPG)  
- 문제점
	* 시간을 측정하는 로직과 핵심 비즈니스의 로직이 섞여서 유지보수가 어렵다.  
	* 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.  
	* 시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다.  

### &#128204; AOP 적용
- AOP: Aspect Oriented Programming  
- 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern) 분리  

![spring img](/assets/spring/8.JPG)  

{% highlight java %}
@Component
@Aspect
public class TimeTraceAop {
	@Around("execution(* hello.hellospring..*(..))")
	public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
		long start = System.currentTimeMillis();
		System.out.println("START: " + joinPoint.toString());
		try {
			return joinPoint.proceed();
		} finally {
			long finish = System.currentTimeMillis();
			long timeMs = finish - start;
			System.out.println("END: " + joinPoint.toString()+ " " + timeMs + "ms");
		}
	}
}
{% endhighlight %}

`@Around()`를 이용해서 원하는 곳만 적용할 수 있습니다!  

<br/>
AOP 적용 전
![spring img](/assets/spring/9.JPG)  
<br/>
AOP 적용 후
![spring img](/assets/spring/10.JPG)  
