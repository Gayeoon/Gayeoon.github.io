---
layout: post
title:  "의존관계 자동 주입"
subtitle:   "의존관계 자동 주입"
categories: spring
tags: Spring DI 
comments: false
---
## 의존관계 자동 주입

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

**&#10024; 컴파일 오류는 세상에서 가장 빠르고, 좋은 오류다!!!&#128518;**

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

> ❓ @Qualifier로 주입할 때 `@Qualifier("mainDiscountPolicy")`를 못찾으면 어떻게 될까?  
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

&#128226; @Primary는 기본값처럼 동작하는 것이고, @Qualifier 는 매우 상세하게 동작하기 때문에 @Qualifier가 우선순위가 더 높다!!  

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

> &#10024; 비즈니스 로직 중에서 다형성을 적극 활용할 때  
> 수동 빈으로 등록하거나 자동으로 ~~굳이~~하고싶다면 **특정 패키지에 같이 묶어**두는게 좋습니다.  

&#128226; 스프링 자체가 자동으로 등록하는 빈들은 그냥 스프링의 의도대로 냅두고 쓰는게 좋다!!  
