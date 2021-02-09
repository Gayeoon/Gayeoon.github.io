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
- ANNOTATION: 기본값, 애노테이션을 인식해서 동작합니다.  
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

<br>
<hr>
### &#128204; 의존관계 주입

#### 생성자 주입
- **생성자**를 통해서 의존 관계를 주입 받는 방법입니다.  
- Bean을 등록할때 호출됩니다.  
- 생성자 호출시점에 딱 1번만 호출되는 것이 보장되며 **불변, 필수 의존관계**에 사용됩니다.  
&#10140; 생성자가 딱 1개만 있으면 @Autowired를 생략해도 **자동 주입** 됩니다! ~~물론 스프링 빈에만 해당~~  

{% highlight java %}
@Component
public class OrderServiceImpl implements OrderService {
	private final MemberRepository memberRepository;
	private final DiscountPolicy discountPolicy;
	
	@Autowired
	public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy;
	}
}
{% endhighlight %}

#### 수정자 주입(setter)
- setter라 불리는 필드의 값을 변경하는 **수정자 메서드**를 통해서 의존관계를 주입하는 방법입니다.  
- **선택, 변경** 가능성이 있는 의존관계에 사용합니다.  
- 자바빈 프로퍼티 규약의 수정자 메서드 방식을 사용하는 방법입니다.  
	+ 자바빈 프로퍼티 규약 : 필드의 값을 직접 변경하지 않고, setXxx, getXxx 메소드를 통해서 값을 읽거나 수정하는 규칙  
- Bean 등록이 끝난 후 의존 관계 주입을 진행할때 등록됩니다.  

{% highlight java %}
@Component
public class OrderServiceImpl implements OrderService {
	private MemberRepository memberRepository;
	private DiscountPolicy discountPolicy;
	
	@Autowired
	public void setMemberRepository(MemberRepository memberRepository) {
		this.memberRepository = memberRepository;
	}

	@Autowired
	public void setDiscountPolicy(DiscountPolicy discountPolicy) {
		this.discountPolicy = discountPolicy;
	}
}
{% endhighlight %}

&#10140; `@Autowired`의 기본 동작은 주입할 대상이 없으면 오류가 발생합니다. 주입할 대상이 없어도 동작하게 하려면 `@Autowired(required = false)`로 지정해야 합니다.  

#### 필드 주입
- 필드에 바로 주입하는 방법입니다.  
- 코드가 간결해지지만 외부에서 변경이 불가능해서 테스트 하기 힘들다는 치명적인 단점이 있습니다.  
	+ 변경을 위해서는 setter를 만들어야 합니다. ~~그럴바엔 생성자 주입을 하는게 좋다..!~~   
- DI 프레임워크가 없으면 아무것도 할 수 없습니다.  
- 사용하지 말자!!!&#128581; ~~안티패턴~~  
	+ 애플리케이션의 실제 코드와 관계 없는 **테스트 코드**  
	+ 스프링 설정을 목적으로 하는 @Configuration 같은 곳에서만 특별한 용도로 사용  

{% highlight java %}
@Component
public class OrderServiceImpl implements OrderService {
	@Autowired 	private MemberRepository memberRepository;
	@Autowired  private DiscountPolicy discountPolicy;
}
{% endhighlight %}

&#10140; 순수한 자바 테스트 코드에는 @Autowired가 동작하지 않기 때문에 @SpringBootTest 처럼 스프링 컨테이너를 테스트에 통합한 경우에만 가능합니다.  

#### 일반 메서드 주입
- 일반 메서드를 통해서 주입하는 방법입니다.  
- 한번에 여러 필드를 주입받을 수 있습니다.  
- 잘 사용하진 않습니다..ㅎ  

{% highlight java %}
@Component
public class OrderServiceImpl implements OrderService {
	private MemberRepository memberRepository;
	private DiscountPolicy discountPolicy;
	
	@Autowired
	public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
		this.memberRepository = memberRepository;
		this.discountPolicy = discountPolicy;
	}
}
{% endhighlight %}

<br>
<hr>
### &#128204; 옵션 처리
주입할 스프링 빈이 없어도 동작해야 할 때가 있습니다. 그런데 `@Autowired`만 사용하면 `required` 옵션의 기본값이 `true` 로 되어 있어서 자동 주입 대상이 없
으면 오류가 발생합니다.  
&#10140; 자동 주입 대상을 옵션으로 처리해야합니다.  

- @Autowired(required=false) : 자동 주입할 대상이 없으면 **수정자 메서드 자체가 호출 안됩니다.**  
- org.springframework.lang.@Nullable : 자동 주입할 대상이 없으면 **null이 입력**됩니다. ~~호출은 됨~~  
- Optional<> : 자동 주입할 대상이 없으면 **Optional.empty가 입력**됩니다.  

{% highlight java %}
//호출 안됨
@Autowired(required = false)
public void setNoBean1(Member member) {
	System.out.println("setNoBean1 = " + member);
}

//null 호출
@Autowired
public void setNoBean2(@Nullable Member member) {
	System.out.println("setNoBean2 = " + member);
}

//Optional.empty 호출
@Autowired(required = false)
public void setNoBean3(Optional<Member> member) {
	System.out.println("setNoBean3 = " + member);
}
{% endhighlight %}
&#10140; Member는 스프링 빈이 아닙니당!  

&#128226; @Nullable, Optional은 스프링 전반에 걸쳐서 지원됩니다.  

<br>
<hr>
### &#128204; 생성자 주입을 선택해야 하는 이유!

#### 불변
- 대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료시점까지 **의존관계를 변경할 일이 없다.** 오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안된다.(불변해야 한다!)  
- 수정자 주입을 사용하면, setXxx 메서드를 public으로 열어두어야 한다.  
- 누군가 실수로 변경할 수도 있고, 변경하면 안되는 메서드를 열어두는 것은 좋은 설계 방법이 아니다.  
- 생성자 주입은 객체를 생성할 때 딱 1번만 호출되므로 이후에 호출되는 일이 없다. 따라서 **불변하게 설계할 수 있다.**  

#### 누락
생성자 주입을 사용하면 **주입 데이터를 누락 했을 때 컴파일 오류가 발생**합니다.  
그리고 IDE에서 바로 어떤 값을 필수로 주입해야 하는지 알 수 있습니다.  
&#10140; 개발자가 의존관게를 파악하기 쉬워집니당!  

#### final 키워드 사용 가능
생성자 주입을 사용하면 필드에 final 키워드를 사용할 수 있습니다.  
&#10140; 생성자에서 혹시라도 값이 설정되지 않는 오류를 **컴파일 시점에 막아줍니다!**  

**&#10024; 컴파일 오류는 세상에서 가장 빠르고, 좋은 오류다!!!&#128518; **

&#128226; 수정자 주입을 포함한 나머지 주입 방식은 모두 생성자 이후에 호출되므로, 필드에 final 키워드를 사용할 수 없습니다. 오직 생성자 주입 방식만 final 키워드를 사용할 수 있습니다!  

#### 정리
- 생성자 주입 방식을 선택하는 이유는 여러가지가 있지만, 프레임워크에 의존하지 않고, 순수한 자바 언어의 특징을 잘 살리는 방법이기도 합니다.  
- 기본으로 생성자 주입을 사용하고, 필수 값이 아닌 경우에는 수정자 주입 방식을 옵션으로 부여하면 됩니다. 생성자 주입과 수정자 주입을 동시에 사용할 수 있습니다.  
&#10024; 항상 생성자 주입을 선택하자! 그리고 가끔 옵션이 필요하면 수정자 주입을 선택하자! 필드 주입은 사용하지 말자!!!  

<br>
<hr>
### &#128204; 롬북과 최신 트랜드
막상 개발을 해보면, 대부분이 다 불변입니다. 그래서 생성자에 final 키워드를 사용하게 됩니다.  
그러다보니 코드가 길어지고 불편해집니다..&#128543; ~~필드 주입은 쉬운데..~~  
  
#### 롬북
- 롬복 라이브러리가 제공하는 @RequiredArgsConstructor 기능을 사용하면 **final이 붙은 필드**를 모아서 **생성자를 자동으로 만들어줍니다.**  

{% highlight java %}
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {
	private final MemberRepository memberRepository;
	private final DiscountPolicy discountPolicy;
}
{% endhighlight %}

&#10024; 롬복이 자바의 애노테이션 프로세서라는 기능을 이용해서 컴파일 시점에 생성자 코드를 자동으로 생성해줍니다.  

#### 롬북 라이브러리 적용 방법

`build.gradle`에 라이브러리 및 환경 추가  

{% highlight java %}
configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}
{% endhighlight %}

{% highlight java %}
dependencies {
	
	...
	
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
	testCompileOnly 'org.projectlombok:lombok'
	testAnnotationProcessor 'org.projectlombok:lombok'
}
{% endhighlight %}

![spring img](/assets/spring/18.JPG) 
File &#10140; Settings &#10140; plugin &#10140; lomvok 검색 &#10140; 설치!!  
~~저는 설치가 되어 있더라고요.. 왜지..? &#128580;~~

![spring img](/assets/spring/19.JPG)
File &#10140; Settings &#10140; Build &#10140; Compiler &#10140; Annotation Processors &#10140; Enable annotation processing 체크!  

<br>
<hr>
### &#128204; 조회 빈이 2개 이상 - 문제
`@Autowired`는 타입(Type)으로 조회합니다.  
{% highlight java %}
@Autowired
private DiscountPolicy discountPolicy
{% endhighlight %}

그렇기때문에 `ac.getBean(DiscountPolicy.class)`와 유사하게 동작합니다.  
타입으로 빈을 조회하면 선택된 빈이 2개 이상일때 문제가 발생합니다.  

&#128226; 물론 하위 타입으로 지정할 수도 있지만, 하위 타입으로 지정하는 것은 **DIP를 위배**하고 **유연성이 떨어집니다.** 그리고 이름만 다르고, 완전히 똑같은 타입의 스프링 빈이 2개 있을 때 해결이 되지 않습니다.  

#### @Autowired 필드 명 매칭
`@Autowired`는 타입 매칭을 시도하고, 이때 여러 빈이 있으면 필드 이름, 파라미터 이름으로 **빈 이름을 추가 매칭합니다.**  

1. 타입 매칭  
2. 타입 매칭의 결과가 2개 이상일 때 **필드 명, 파라미터 명**으로 빈 이름 매칭  

&#128226; 필드 명 매칭은 먼저 타입 매칭을 시도 하고 그 결과에 여러 빈이 있을 때 추가로 동작하는 기능입니다!  

#### @Qualifier 사용
@Qualifier 는 추가 구분자를 붙여주는 방법입니다. 주입 시 **추가적인 방법을 제공하는 것**이지 빈 이름을 변경하는 것은 아닙니다.  

빈 등록시 @Qualifier를 붙여 줍니다.  
{% highlight java %}
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}
{% endhighlight %}

주입시에 @Qualifier를 붙여주고 등록한 이름을 적어줍니다.   
- 생성자 자동 주입 예시  
{% highlight java %}
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, @Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy) {
	this.memberRepository = memberRepository;
	this.discountPolicy = discountPolicy;
}
{% endhighlight %}

❓ @Qualifier로 주입할 때 `@Qualifier("mainDiscountPolicy")`를 못찾으면 어떻게 될까?  
> 그러면 mainDiscountPolicy라는 이름의 스프링 빈을 추가로 찾습니다. 하지만 @Qualifier는 @Qualifier를 찾는 용도로만 사용하는게 명확하고 좋습니다.  

{% highlight java %}
@Bean
@Qualifier("mainDiscountPolicy")
public DiscountPolicy discountPolicy() {
 return new ...
}
{% endhighlight %}
&#10024; 빈 등록시에도 @Qualifier를 동일하게 사용할 수 있습니다.  

**@Qualifier 정리**  
1. @Qualifier끼리 매칭  
2. 빈 이름 매칭  
3. NoSuchBeanDefinitionException 예외 발생  

&#128226; @Qualifier는 주입 받을 때 다음과 같이 모든 코드에 @Qualifier 를 붙여주어야 한다는 단점이 있습니다.  

#### @Primary 사용
@Primary는 **우선순위를 정하는 방법**입니다. @Autowired 시에 여러 빈이 매칭되면 @Primary가 우선권을 가집니다.  

**@Primary, @Qualifier 활용**  
> 코드에서 자주 사용하는 메인 데이터베이스의 커넥션을 획득하는 스프링 빈이 있고, 코드에서 특별한 기능으로 가끔 사용하는 서브 데이터베이스의 커넥션을 획득하는 스프링 빈이 있다고 생각해보자. 
**메인 데이터베이스의 커넥션을 획득하는 스프링 빈은 @Primary 를 적용**해서 조회하는 곳에서 @Qualifier 지정 없이 
편리하게 조회하고, **서브 데이터베이스 커넥션 빈을 획득할 때는 @Qualifier를 지정**해서 명시적으로 
획득 하는 방식으로 사용하면 코드를 깔끔하게 유지할 수 있습니다. 물론 이때 메인 데이터베이스의 스프링 빈을 등록할 때 
@Qualifier 를 지정해주는 것은 상관없습니다.  

**우선순위**  
> @Primary는 기본값처럼 동작하는 것이고, @Qualifier 는 매우 상세하게 동작하기 때문에 @Qualifier가 우선순위가 더 높다!!  

<br>
<hr>
### &#128204; 자동, 수동의 올바른 실무 운영 기준

##### 업무 로직 빈
- 웹을 지원하는 컨트롤러, 핵심 비즈니스 로직이 있는 서비스, 데이터 계층의 로직을 처리하는 리포지토리 등  
- 보통 비즈니스 요구사항을 개발할 때 추가되거나 변경됩니다.  
- 숫자도 매우 많고, 한번 개발해야 하면 컨트롤러, 서비스, 리포지토리 처럼 어느정도 유사한 패턴이 있습니다.  
- 어디가 문제인지 명확하게 잘 들어납니다.  
&#10024; 자동 기능을 적극 사용하는 것이 좋다!!  

##### 기술 지원 빈
- 기술적인 문제나 공통 관심사(AOP)를 처리할 때 주로 사용됩니다.  
- 데이터베이스 연결이나, 공통 로그 처리 처럼 업무 로직을 지원하기 위한 하부 기술이나 공통 기술들  
- 업무 로직과 비교해서 그 수가 매우 적고, 보통 애플리케이션 전반에 걸쳐서 광범위하게 영향을 미칩니다.  
- 적용이 잘 되고 있는지 아닌지 조차 파악하기 어려운 경우가 많습니다.  
&#10140; 수동 빈 등록을 사용해서 명확하게 들어내는 것이 좋다!!  

&#10024; 비즈니스 로직 중에서 다형성을 적극 활용할 때  
> 수동 빈으로 등록하거나 자동으로 ~~굳이~~하고싶다면 **특정 패키지에 같이 묶어**두는게 좋습니다.  

&#128226; 스프링 자체가 자동으로 등록하는 빈들은 그냥 스프링의 의도대로 냅두고 쓰는게 좋다!!  

