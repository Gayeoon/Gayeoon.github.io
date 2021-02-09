---
layout: post
title:  "Spring 기본"
subtitle:   "Spring 기본"
categories: spring
tags: Spring SOLID IOC DI 
comments: false
---
## Spring 기본

### &#128204; SOLID
객체지향 설계의 5대 원칙  
- SPR(Single Responsibility Principle) : 단일 책임 원칙  
	+ 어떤 클래스를 변경하는 이유는 오직 하나 뿐이어야 한다.  
	+ 한 클래스는 하나의 책임만을 가져야 한다. &#10140; 관심사를 분리!  
	+ AppConfig : 객체 생성 및 연결 , 클라이언트 : 실행
- OCP(Open Closed Principle) : 개방 폐쇄 원칙  
	+ 소프트웨어 엔티티(클래스, 모듈, 함수 등)는 확장에 대해서는 열려 있어야 하지만 변경에 대해서는 닫혀 있어야 한다. 즉, 자신의 확장에는 열려있고, 주변의 변화에 대해서는 닫혀 있어야 한다.  
	+ interface를 통해 구현하여 해결할 수 있습니다.   
	+ 어플리케이션을 사용영역과 구현영역으로 나누면 소프트웨어 요소를 새롭게 확장해도 사용영역의 변경의 닫혀있게 됩니다!  
- LSP(Liskov Substitution Principle) : 리스코프 치환 원칙  
	+ 서브타입은 언제나 자신의 기반타입으로 교체할 수 있어야 한다.  
	+ 즉, 하위 클래스의 인스턴스는 상위형 객체 참조 변수에 대입해 상위 클래스의 인스턴스 역할을 수행하는 데 문제가 없어야 한다.  
	+ 상속, 인터페이스의 원칙이 잘 지켜진다면 LSP는 자동으로 적용된다.  
- ISP(Interface Segregation Principle) : 인터페이스 분리 원칙  
	+ 클라이언트는 자신이 사용하지 않는 메소드에 의존 관계를 맺으면 안된다.  
- DIP(Dependency Inversion Principle) : 의존 역전 원칙  
	* 고차원 모듈은 저차원 모듈에 의존하면 안된다. 추상화된 것은 구체적인 것에 의존하면 안된다. 구체적인 것이 추상화된 것에 의존해야 한다.  
	* 자주 변경되는 클래스에 의존하지 말자!    
	* AppConfig가 객체 인스턴스를 클라이언트 코드 대신 생성해서 클라이언트 코드에 의존관계를 주입시키면 된다.  
<br>
<hr>
### &#128204; AppConfig
어플리케이션의 전체 동작 방식을 구성(config)하기 위해, **구현 객체를 생성**하고, **연결**하는 책임을 가지는 별도의 설정 클래스  
- 어플리케이션의 실제 동작에 필요한 **구현 객체를 생성**합니다.  
- 생성한 객체 인스턴스의 참조(레퍼런스)를 **생성자를 통해서 연결**해줍니다.  
&#10140; 관심사의 분리 : 객체를 생성하고 연결하는 역할과 실행하는 역할을 분리하는 것!  

{% highlight java %}
public class AppConfig {
	public MemberService memberService() {
		return new MemberServiceImpl(new MemoryMemberRepository());
	}
	
	public OrderService orderService() {
		return new OrderServiceImpl(
			new MemoryMemberRepository(),
			new FixDiscountPolicy());
	}
}
{% endhighlight %}

MemberServiceImpl - 생성자 주입  

{% highlight java %}
public class MemberServiceImpl implements MemberService {
	private final MemberRepository memberRepository;
	
	public MemberServiceImpl(MemberRepository memberRepository) {
		this.memberRepository = memberRepository;
	}
}
{% endhighlight %}

OrderServiceImpl - 생성자 주입 (두개!)  

{% highlight java %}
public class OrderServiceImpl implements OrderService {
	private final MemberRepository memberRepository;
	private final DiscountPolicy discountPolicy;
	
	public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy;
	}
}
{% endhighlight %}

### &#128204; JUnit Test

{% highlight java %}
class MemberServiceTest {
	MemberService memberService;
	
	@BeforeEach
	public void beforeEach() {
		AppConfig appConfig = new AppConfig();
		memberService = appConfig.memberService();
	}
}
{% endhighlight %}
<br>
<hr>

### &#128204; AppConfig 리팩터링
생성자를 호출하는 중복된 코드를 줄여봅시다!!  
`return new MemberServiceImpl(new MemoryMemberRepository());` 요렇게 호출했던 것을 메소드로 분리합니다.  

{% highlight java %}
public class AppConfig {
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    public OrderService orderService() {
        return new OrderServiceImpl(
                memberRepository(),
                discountPolicy());
    }

    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    public DiscountPolicy discountPolicy() {
        return new FixDiscountPolicy();
    }
}
{% endhighlight %}

중복이 제거되었습니다.&#128582;  
구성 정보에서 역할과 구현을 명확하게 분리하면서 역할이 잘 들어나게 되었습니당ㅎㅎ  

<br>
<hr>

### &#128204; IoC (Inversion of Control) : 제어의 역전
프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)이라 합니다.  
프로그램에 대한 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있습니다.  

- 프레임워크 VS 라이브러리  
	- 프레임워크가 내가 작성한 **코드를 제어하고, 대신 실행하면** 그것은 프레임워크가 맞다. (JUnit)  
	- 반면에 내가 작성한 코드가 **직접 제어의 흐름을 담당**한다면 그것은 프레임워크가 아니라 라이브러리다.  
	
### &#128204; DI (Dependency Injection) : 의존관계 주입
- 정적인 클래스 의존관계  
	* 클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있습니다.  
	* 정적인 의존관계는 어플리케이션을 실행하지 않아도 분석할 수 있습니다.  
	&#10140; 하지만 이러한 클래스 의존관계 만으로는 실제 어떤 객체가 주입 될지 알 수 없습니다.  
- 동적인 객체 인스턴스 의존 관계  
	* 어플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계입니다.  

어플리케이션 **실행 시점(런타임)**에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결 되는 것을 **의존관계 주입**이라 합니다.  
* 의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있습니다.  
* 의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있습니다.  

### &#128204; IoC 컨테이너, DI 컨테이너
AppConfig 처럼 객체를 생성하고 관리하면서 의존관계를 연결해 주는 것을 IoC 컨테이너 또는 DI 컨테이너라 합니다.  
의존관계 주입에 초점을 맞추어 최근에는 주로 **DI 컨테이너**라 합니다.  

<br>
<hr>
### &#128204; @ComponentScan
![spring img](/assets/spring/16.JPG)  
@ComponentScan은 @Component가 붙은 모든 클래스를 스프링 빈으로 등록합니다.  
이때 스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용합니다.  
- 빈 이름 기본 전략: MemberServiceImpl 클래스 memberServiceImpl  
- 빈 이름 직접 지정: 만약 스프링 빈의 이름을 직접 지정하고 싶으면 `@Component("memberService2")` 이런식으로 이름을 부여하면 됩니다.  

### &#128204; @Autowired
![spring img](/assets/spring/17.JPG)  
생성자에 @Autowired를 지정하면, 스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 주입합니다.  
이때 기본 조회 전략은 타입이 같은 빈을 찾아서 주입합니다.  
- `getBean(MemberRepository.class)`와 동일합니다.  

<br>
<hr>
### &#128204; 탐색 위치 지정
모든 자바 클래스를 다 컴포넌트 스캔하면 시간이 오래 걸리기 때문에 시작 위치를 정해서 필요한 위치부터 탐색하도록 해야 합니다.  

{% highlight java %}
@ComponentScan(basePackages = "hello.core",}
{% endhighlight %}

- basePackages : **탐색할 패키지의 시작 위치를 지정**합니다. 이 패키지를 포함해서 하위 패키지를 모두 탐색합니다.  
	* basePackages = {"hello.core", "hello.service"} 이렇게 여러 시작 위치를 지정할 수도 있습니다.  
- basePackageClasses : 지정한 **클래스의 패키지**를 탐색 시작 위치로 지정합니다.  
- 만약 지정하지 않으면 @ComponentScan이 붙은 설정 정보 클래스의 패키지가 시작 위치가 됩니다.  

&#128226; 패키지 위치를 지정하지 말고, 설정 정보 클래스의 위치를 프로젝트 **최상단**에 두자!  
스프링 부트를 사용하면 스프링 부트의 대표 시작 정보인 @SpringBootApplication를 이 프로젝트 시작 루트 위치에 두는 것이 관례입니다. (그리고 이 설정안에 바로 @ComponentScan 이 들어있습니다!)  

### &#128204; 컴포넌트 스캔 기본 대상
컴포넌트 스캔은 @Component 뿐만 아니라 다음과 내용도 추가로 대상에 포함합니다.  
- @Component : 컴포넌트 스캔에서 사용 스프링  
- @Controlller : 스프링 MVC 컨트롤러에서 사용  
	MVC 컨트롤러로 인식합니다.  
- @Service : 스프링 비즈니스 로직에서 사용  
	비즈니스 계층을 인식하는데 도움을 줍니다.  
- @Repository : 스프링 데이터 접근 계층에서 사용  
	스프링 데이터 접근 계층으로 인식하고, 데이터 계층의 예외를 스프링 예외로 변환합니다.  
- @Configuration : 스프링 설정 정보에서 사용  
	스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤을 유지하도록 추가 처리를 합니다.  

&#128226; 어노테이션에는 상속관계라는 것이 없습니다. 그래서 이렇게 어노테이션이 특정 어노테이션을 들고 있는 것을 인식할 수 있는 것은 자바 언어가 지원하는 기능은 아니고, 스프링이 지원하는 기능입니다.  

{% highlight java %}
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyIncludeComponent {}

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {}
{% endhighlight %}

{% highlight java %}
@ComponentScan(
	includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
	excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
)
{% endhighlight %}

includeFilters에 MyIncludeComponent 어노테이션을 추가해서 BeanA가 스프링 빈에 등록됩니다.  
excludeFilters에 MyExcludeComponent 어노테이션을 추가해서 BeanB는 스프링 빈에 등록되지 않습니다.  

### &#128204; FilterType
- ANNOTATION: 기본값, 어노테이션을 인식해서 동작합니다.  
	ex) `org.example.SomeAnnotation`  
- ASSIGNABLE_TYPE: 지정한 타입과 자식 타입을 인식해서 동작합니다.  
	ex) `org.example.SomeClass`  
- ASPECTJ: AspectJ 패턴 사용  
	ex) `org.example..*Service+`  
- REGEX: 정규 표현식  
	ex) `org\.example\.Default.*`  
- CUSTOM: TypeFilter 이라는 인터페이스를 구현해서 처리합니다.  
	ex) `org.example.MyTypeFilter`  
	
<br>
<hr>
### &#128204; 중복 등록과 충돌

#### 자동 빈 등록 vs 자동 빈 등록
컴포넌트 스캔에 의해 자동으로 스프링 빈이 등록되는데, 그 이름이 같은 경우 스프링은 오류를 발생시킵니다.  
	`ConflictingBeanDefinitionException` 예외 발생 ~~거의 없음&#128581;~~  

#### 수동 빈 등록 vs 자동 빈 등록
수동 빈 등록이 우선권을 가집니다.  
&#128226; 수동 빈이 자동 빈을 오버라이딩 해버립니다.  

{% highlight text %}
Overriding bean definition for bean 'memoryMemberRepository' with a different
definition: replacing
{% endhighlight %}

하지만 현실은 의도한 것이 아닌 여러 설정이 꼬이면서 이런 결과가 발생합니다.&#128543;  

{% highlight text %}
Consider renaming one of the beans or enabling overriding by setting
spring.main.allow-bean-definition-overriding=true
{% endhighlight %}

그래서 최근 스프링부트에서는 다음과 같은 오류가 발생하도록 기본값을 바꾸었습니다.  
