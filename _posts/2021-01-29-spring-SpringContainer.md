---
layout: post
title:  "Spring Container"
subtitle:   "Spring Container"
categories: spring
tags: Spring Container Bean Singleton
comments: false
---
## Spring Container and Spring Bean

### &#128204; 스프링 컨테이너

{% highlight java %}
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
{% endhighlight %}

- @Configuration : AppConfig에 설정을 구성한다는 뜻  
- @Bean : 스프링 컨테이너에 스프링 빈으로 등록한다는 뜻  
- ApplicationContext를 스프링 컨테이너라 합니다.  
- 스프링 컨테이너는 XML을 기반으로 만들 수 있고, 애노테이션 기반의 자바 설정 클래스로 만들 수 있습니다.  
	&#10140; 요즘은 어노테이션 기반으로만 만든다!!  
- 기존에는 개발자가 직접 자바코드로 모든 것을 했다면 이제부터는 스프링 컨테이너에 객체를 스프링 빈으로 등록하고, 스프링 컨테이너에서 스프링 빈을 찾아서 사용하도록 변경되었습니다.  

### &#128204; 스프링 컨테이너 생성 과정

![spring img](/assets/spring/11.JPG)  

- new AnnotationConfigApplicationContext(AppConfig.class)  
- 스프링 컨테이너를 생성할 때는 구성 정보를 지정해주어야 합니다.  
- 여기서는 AppConfig.class 를 구성 정보로 지정했습니다.  
- 스프링 컨테이너는 파라미터로 넘어온 설정 클래스 정보를 사용해서 스프링 빈을 등록합니다.  

<br>
<hr>
### &#128204; 스프링 빈 의존관계

![spring img](/assets/spring/12.JPG)  
- 스프링 컨테이너는 설정 정보를 참고해서 의존관계를 주입(DI)합니다.  

#### 빈 이름
- 빈 이름은 메서드 이름을 사용합니다.  
- 빈 이름을 직접 부여할 수도 있습니다.  
	- @Bean(name="gayeonService")  
&#128226; 빈 이름은 항상 다른 이름을 부여해야 합니다. 같은 이름을 부여하면, 다른 빈이 무시되거나, 기존 빈을 덮어버리거나 설정에 따라 오류가 발생합니다.  

### &#128204; 빈 조회하기

#### 모든 빈 출력하기

{% highlight java %}
void findAllBean() {
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();
    for (String beanDefinitionName : beanDefinitionNames) {
        Object bean = ac.getBean(beanDefinitionName);
        System.out.println("name=" + beanDefinitionName + " object=" + bean);
    }
}
{% endhighlight %}

- 실행하면 스프링에 등록된 모든 빈 정보를 출력할 수 있습니다.  
- ac.getBeanDefinitionNames() : 스프링에 등록된 모든 빈 이름을 조회합니다.  
- ac.getBean() : 빈 이름으로 빈 객체(인스턴스)를 조회합니다.  

#### 애플리케이션 빈 출력하기

{% highlight java %}
void findApplicationBean() {
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();
    
	for (String beanDefinitionName : beanDefinitionNames) {
        BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);
        
		if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
			Object bean = ac.getBean(beanDefinitionName);
            System.out.println("name=" + beanDefinitionName + " object=" + bean);
        }
    }
}
{% endhighlight %}

- 스프링이 내부에서 사용하는 빈은 제외하고, 내가 등록한 빈만 출력할 수 있습니다.  
- 스프링이 내부에서 사용하는 빈은 getRole()로 구분할 수 있습니다.  
- ROLE_APPLICATION : 일반적으로 사용자가 정의한 빈  
- ROLE_INFRASTRUCTURE : 스프링이 내부에서 사용하는 빈  

#### 이름으로 빈 조회하기
- 스프링 컨테이너에서 스프링 빈을 찾는 가장 기본적인 조회 방법  
	- ac.getBean(빈이름, 타입)  
	- ac.getBean(타입)  
- 조회 대상 스프링 빈이 없으면 예외 발생  
	- NoSuchBeanDefinitionException: No bean named 'xxxxx' available  
	
{% highlight java %}
MemberService memberService = ac.getBean("memberService", MemberService.class);
assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
{% endhighlight %}

#### 타입으로 빈 조회하기

{% highlight java %}
MemberService memberService = ac.getBean(MemberService.class);
assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
{% endhighlight %}

#### 구체적인 타입으로 빈 조회하기

{% highlight java %}
MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
{% endhighlight %}

- 타입으로 조회시 같은 타입이 둘 이상 있으면 중복 오류 발생  
	- NoUniqueBeanDefinitionException  
	&#128226; 타입이 여러개일 경우 빈 이름을 지정하자!  

#### 상속 관계
- 부모타입으로 조회하면 자식 타입도 합께 조회합니다.  
- Object 타입으로 조회하면 모든 스프링 빈을 조회할 수 있습니다.  
  
{% highlight java %}
Map<String, Object> beansOfType = ac.getBeansOfType(Object.class);
for (String key : beansOfType.keySet()) {
    System.out.println("key = " + key + " value=" + beansOfType.get(key));
}
{% endhighlight %}

<br>
<hr>
### &#128204; BeanFactory
- 스프링 컨테이너의 최상위 인터페이스입니다.  
- **스프링 빈**을 **관리**하고 **조회**하는 역할을 담당합니다.  
- getBean()을 제공합니다.  

### &#128204; ApplicationContext
- BeanFactory 기능을 모두 상속받아서 제공합니다.  
- 빈 관리기능 + 편리한 부가 기능을 제공합니다.  
	* 메시지소스를 활용한 국제화 기능(MessageSource)  
		+ 예를 들어서 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력  
	* 환경변수(EnvironmentCapable)  
		+ 로컬, 개발(Test 서버), 운영등을 구분해서 처리  
	* 애플리케이션 이벤트(ApplicationEventPublisher)  
		+ 이벤트를 발행하고 구독하는 모델을 편리하게 지원  
	* 편리한 리소스 조회(ResourceLoader)  
		+ 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회  
		
<br>
<hr>
### &#128204; BeanDefinition
- 빈 설정 메타정보  
	* @Bean 당 각각 하나씩 메타 정보가 생성됩니다.  
- 역할과 구현을 개념적으로 나눈 것입니다.  
- 스프링 컨테이너는 메타정보를 기반으로 스프링 빈을 생성합니다.  
- BeanDefinition 정보  
	+ BeanClassName: 생성할 빈의 클래스 명(자바 설정 처럼 팩토리 역할의 빈을 사용하면 없음)  
	+ factoryBeanName: 팩토리 역할의 빈을 사용할 경우 이름, *ex) appConfig*  
	+ factoryMethodName: 빈을 생성할 팩토리 메서드 지정, *ex) memberService*  
	+ Scope: 싱글톤(기본값)  
	+ lazyInit: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때 까지 최대한 생성을 지연처리 하는지 여부  
	+ InitMethodName: 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명  
	+ DestroyMethodName: 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명  
	+ Constructor arguments, Properties: 의존관계 주입에서 사용한다. (자바 설정 처럼 팩토리 역할의 빈을 사용하면 없음)  

&#10140; 스프링이 다양한 형태의 설정 정보를 BeanDefinition으로 추상화해서 사용한다!!  

<br>
<hr>
### &#128204; 순수한 DI 컨테이너
- 스프링이 없는 순수한 DI 컨테이너인 AppConfig는 요청을 할 때 마다 객체를 새로 생성합니다.  
	&#10140; 요청할때마다 매번 객체를 생성하기 때문에 메모리 낭비가 심하다!!&#128543;  
&#10140; 싱글톤 패턴을 이용하여 해당 객체가 딱 1개만 생성되고, 공유하도록 설계해야 합니다.  

![spring img](/assets/spring/13.JPG)  

### &#128204; 싱글톤 패턴
- 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴입니다.  
- 객체 인스턴스를 2개 이상 생성하지 못하도록 막아야 합니다.  
	&#10140; **private 생성자**를 사용해서 외부에서 임의로 new 키워드를 사용하지 못하도록 막아야 합니다!&#128581;  
- 싱글톤 패턴을 적용하면 고객의 요청이 올 때 마다 객체를 생성하는 것이 아니라, 이미 만들어진 **객체를 공유**해서 효율적으로 사용할 수 있습니다.  
- 싱글톤 패턴 문제점  
	+ 싱글톤 패턴을 구현하는 코드 자체가 많이 들어갑니다.  
	+ 의존관계상 클라이언트가 구체 클래스에 의존합니다. **DIP 위반**  
	+ 클라이언트가 구체 클래스에 의존해서 **OCP 원칙을 위반**할 가능성이 높습니다.  
	+ 테스트하기 어렵습니다.  
	+ 내부 속성을 변경하거나 초기화 하기 어렵습니다.  
	+ private 생성자로 **자식 클래스를 만들기 어렵습니다.**  
	+ 결론적으로 **유연성**이 떨어집니다.  
	+ **안티패턴**으로 불리기도 합니다.  

### &#128204; 싱글톤 컨테이너
- 스프링 컨테이너는 싱글턴 패턴을 적용하지 않아도, 객체 인스턴스를 싱글톤으로 관리합니다.  
- 스프링 컨테이너는 싱글톤 컨테이너 역할을 합니다.  
- 이렇게 싱글톤 객체를 생성하고 관리하는 기능을 **싱글톤 레지스트리**라 합니다.  
- 스프링 컨테이너의 이런 기능 덕분에 싱글턴 패턴의 모든 단점을 해결하면서 객체를 싱글톤으로 유지할 수 있습니다.  
- 싱글톤 패턴을 위한 지저분한 코드가 들어가지 않아도 됩니다.  
- DIP, OCP, 테스트, private 생성자로 부터 자유롭게 싱글톤을 사용할 수 있습니다.  

![spring img](/assets/spring/14.JPG)  

&#128226; 스프링의 기본 빈 등록 방식은 싱글톤이지만, 싱글톤 방식만 지원하는 것은 아닙니다. 요청할 때 마다 새로운 객체를 생성해서 반환하는 기능도 제공합니다!!  
~~그치만 99.9%는 싱글톤 방식을 사용합니다ㅎ~~  

### &#128204; 싱글톤 방식의 주의점
&#10140; 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안됩니다.  
- 무상태(stateless)로 설계해야 합니다.  
	+ 특정 클라이언트에 의존적인 필드가 있으면 안됩니다.  
	+ 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안됩니다.  
	+ 가급적 읽기만 가능해야 합니다.  
	+ 필드 대신에 자바에서 공유되지 않는 **지역변수, 파라미터, ThreadLocal** 등을 사용해야 합니다.  
- **스프링 빈의 필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있습니다!!!**  

<br>
<hr>
### &#128204; @Configuration과 싱글톤
AppConfig 스프링 빈을 조회해서 클래스 정보를 출력해봅시다. *ex)bean.getClass()*  
순수한 클래스는 `class hello.core.AppConfig`로 출력됩니다.  

{% highlight java %}
bean = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$713ccfe9
{% endhighlight %}

하지만 클래스 명 뒤에 xxCGLIB가 붙은 것을 확인할 수 있습니다!!&#128559;  
&#128226; 이것은 스프링이 CGLIB라는 바이트코드 조작 라이브러리를 사용해서 AppConfig 클래스를 상속받은 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록했기 때문입니다.  

![spring img](/assets/spring/15.JPG) 

CGLIB는 @Bean이 붙은 메서드마다 이미 **스프링 빈이 존재하면 존재하는 빈을 반환**하고, **스프링 빈이 없으면 생성**해서 스프링 빈으로 등록하고 반환하는 코드를 동적으로 만듭니다.  
그 덕분에 스프링은 싱글톤이 보장될 수 있습니다!!&#128582;  

&#128226; @Configuration을 적용하지 않고 @Bean만 적용하면 싱글톤이 보장되지 않습니다!&#128581;  


