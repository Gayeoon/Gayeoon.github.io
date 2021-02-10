---
layout: post
title:  "Bean Scope"
subtitle:   "Bean Scope"
categories: spring
tags: Bean Scope
comments: false
---
## Bean Scope

**Spring Scope**
- 싱글톤: 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 **가장 넓은 범위**의 스코프입니다.  
- 프로토타입: 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 **매우 짧은 범위**의 스코프입니다.  
- 웹 관련 스코프  
	+ request: 웹 요청이 들어오고 나갈때 까지 유지되는 스코프입니다.  
	+ session: 웹 세션이 생성되고 종료될 때 까지 유지되는 스코프입니다.  
	+ application: 웹의 서블릿 컨텍스와 같은 범위로 유지되는 스코프입니다.  

### &#128204; 프로토타입 스코프
프로토타입 스코프를 스프링 컨테이너에 조회하면 스프링 컨테이너는 항상 **새로운 인스턴스를 생성**해서 반환합니다.  

#### 싱글톤 빈 요청
![spring img](/assets/spring/20.JPG)  
1. 싱글톤 스코프의 빈을 스프링 컨테이너에 요청한다.  
2. 스프링 컨테이너는 **본인이 관리하는 스프링 빈**을 반환한다.  
3. 이후에 스프링 컨테이너에 같은 요청이 와도 **같은 객체 인스턴스의 스프링 빈을 반환**한다.  

#### 프로토타입 빈 요청1
![spring img](/assets/spring/21.JPG)  
1. 프로토타입 스코프의 빈을 스프링 컨테이너에 요청한다.
2. 스프링 컨테이너는 이 시점에 **프로토타입 빈을 생성**하고, **필요한 의존관계를 주입**한다.

#### 프로토타입 빈 요청2
![spring img](/assets/spring/22.JPG)  
3. 스프링 컨테이너는 생성한 프로토타입 빈을 클라이언트에 반환한다.
4. 이후에 스프링 컨테이너에 같은 요청이 오면 **항상 새로운 프로토타입 빈을 생성해서 반환**한다.

##### 프로토타입 빈의 특징
- 스프링 컨테이너에 요청할 때마다 새로 생성됩니다.  
- 스프링 컨테이너는 **프로토타입 빈의 생성과 의존관계 주입 그리고 초기화까지만 관여**합니다. 
- 클라이언트에 빈을 반환하고, 이후 스프링 컨테이너는 생성된 프로토타입 빈을 관리하지 않습니다. 
- @PreDestory 같은 종료 메서드가 호출되지 않습니다.  
- 그래서 프로토타입 빈은 프로토타입 빈을 조회한 클라이언트가 관리해야 합니다. 종료 메서드에 대한 호출도 클라이언트가 직접 해야합니다.  

<br>
<hr>
### &#128204; 싱글톤 빈과 함께 사용시 문제점
count 증가 로직으로 프로토타입 스코프가 싱글톤 빈과 함께 사용했을때의 문제점을 알아봅시다!!  

#### 프로토타입 빈 직접 요청 상황 (client 한명)
![spring img](/assets/spring/23.JPG)  
1. 클라이언트A는 스프링 컨테이너에 프로토타입 빈을 요청한다.  
2. 스프링 컨테이너는 프로토타입 빈을 **새로 생성해서 반환(x01)한다.** 해당 빈의 count 필드 값은 0이다.  
3. 클라이언트는 조회한 프로토타입 빈에 `addCount()`를 호출하면서 count 필드를 +1 한다.  
&#10140; 결과적으로 프로토타입 빈(x01)의 **count는 1이 된다.**  

#### 프로토타입 빈 직접 요청 상황 (client 여러명)
![spring img](/assets/spring/24.JPG)  
1. 클라이언트B는 스프링 컨테이너에 프로토타입 빈을 요청한다.  
2. 스프링 컨테이너는 프로토타입 빈을 **새로 생성해서 반환(x02)한다.** 해당 빈의 count 필드 값은 0이다.  
3. 클라이언트는 조회한 프로토타입 빈에 addCount() 를 호출하면서 count 필드를 +1 한다.  
&#10140; 결과적으로 프로토타입 빈(x02)의 **count는 1이 된다.**  

&#128226; 프로토타입 빈을 진접 요청할때는 문제가 없다!!  
그렇다면 clientBean이라는 싱글톤 빈이 DI를 통해서 프로토타입 빈을 주입받아 사용하는 상황을 보자!  

#### 싱글톤 빈에서 프로토타입 빈 사용 (client 한명)
![spring img](/assets/spring/25.JPG)  
clientBean은 싱글톤이므로, 보통 스프링 컨테이너 **생성 시점에 함께 생성**되고, 의존관계 주입도 발생한다.  
1. clientBean은 의존관계 자동 주입을 사용한다. 주입 시점에 스프링 컨테이너에 프로토타입 빈을 요청한다.  
2. 스프링 컨테이너는 프로토타입 빈을 생성해서 clientBean에 반환한다. 프로토타입 빈의 count 필드 값은 0이다.  
이제 clientBean 은 프로토타입 빈을 **내부 필드에 보관**한다. ~~(정확히는 참조값을 보관한다.)~~  

![spring img](/assets/spring/26.JPG)  
클라이언트 A는 clientBean을 스프링 컨테이너에 요청해서 받는다. 싱글톤이므로 **항상 같은 clientBean이 반환**된다.  
3. 클라이언트 A는 `clientBean.logic()`을 호출한다.  
4. clientBean 은 prototypeBean의 `addCount()`를 호출해서 프로토타입 빈의 count를 증가한다.  
&#10140; count값이 1이 된다.  

#### 싱글톤 빈에서 프로토타입 빈 사용 (client 여러명)
![spring img](/assets/spring/27.JPG)  
클라이언트 B는 clientBean 을 스프링 컨테이너에 요청해서 받는다. 싱글톤이므로 **항상 같은 clientBean이 반환**된다. ~~즉 A와 같은 clientBean이 반환된다&#128561;~~
clientBean이 내부에 가지고 있는 프로토타입 빈은 이미 과거에 주입이 끝난 빈이다. (사용 할 때마다 새로 생성 X)  
5. 클라이언트 B는 `clientBean.logic()`을 호출한다.  
6. clientBean은 prototypeBean의 `addCount()`를 호출해서 프로토타입 빈의 count를 증가한다.  
&#10140; 원래 count 값이 1이었으므로 2가 된다.&#128565;  

&#128226; 싱글톤 빈은 생성 시점에만 의존관계 주입을 받기 때문에 프로토타입 빈이 새로 생성되긴 하지만, 싱글톤 빈과 함께 계속 유지되면서 문제가 생긴다.  
&#10140; 로직 돌릴때마다 프로토타입 빈을 호출하면 ~~무식하게~~ 문제를 해결할 수는 있다..!!  
- DL(의존관계 조회) : 직접 필요한 의존관계를 찾는 것  

<br>
<hr>
### &#128204; 프로토타입 스코프 - Provider

#### ObjectProvider
{% highlight java %}
@Autowired
private ObjectProvider<PrototypeBean> prototypeBeanProvider;
public int logic() {
	PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
	prototypeBean.addCount();
	int count = prototypeBean.getCount();
	return count;
}
{% endhighlight %}
- `prototypeBeanProvider.getObject()`을 통해서 항상 새로운 프로토타입 빈을 생성합니다.  
- ObjectProvider의 getObject()를 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환합니다.  
- **스프링이 제공하는 기능을 사용**하지만, 기능이 단순하므로 단위테스트를 만들거나 mock 코드를 만들기는 훨씬 쉬워집니다.  
- ObjectProvider는 지금 딱 필요한 **DL 정도의 기능만 제공**합니다.  
<br/>
- ObjectFactory: 기능이 단순, 별도의 라이브러리 필요 없음, 스프링에 의존  
- ObjectProvider: ObjectFactory 상속, 옵션, 스트림 처리등 편의 기능이 많고, 별도의 라이브러리 필요 없음, 스프링에 의존  

#### JSR-330 Provider
마지막 방법은 `javax.inject.Provider`라는 JSR-330 자바 표준을 사용하는 방법입니다.  
이 방법을 사용하려면 `javax.inject:javax.inject:1` 라이브러리를 gradle에 추가해야 합니다.  

{% highlight java %}
package javax.inject;

public interface Provider<T> {
	T get();
}
{% endhighlight %}
javax.inject.Provider 참고용 코드


{% highlight java %}
@Autowired
private Provider<PrototypeBean> provider;

public int logic() {
	PrototypeBean prototypeBean = provider.get();
	prototypeBean.addCount();
	int count = prototypeBean.getCount();
	return count;
}
{% endhighlight %}
- `provider.get()`을 통해서 항상 새로운 프로토타입 빈을 생성합니다.  
- provider의 get()을 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환합니다.  
- **자바 표준**이고, 기능이 단순하므로 단위테스트를 만들거나 mock 코드를 만들기는 훨씬 쉬워집니다.
- Provider는 지금 딱 필요한 **DL 정도의 기능만 제공**한다.

##### 특징
- get() 메서드 하나로 기능이 매우 단순합니다.  
- 별도의 라이브러리가 필요합니다.  
- 자바 표준이므로 스프링이 아닌 다른 컨테이너에서도 사용할 수 있습니다.  

> ❓ 프로토타입 빈은 언제 사용할까?
> 매번 사용할 때 마다 의존관계 주입이 완료된 새로운 객체가 필요할때 사용합니다.
> 하지만 실무에선 대부분 싱글톤 빈으로 문제를 해결할 수 있기 때문에 사용하는 일은 없습니당..ㅎ

<br>
<hr>
### &#128204; 웹 스코프
웹 스코프는 프로토타입과 다르게 스프링이 해당 스코프의 종료시점까지 관리합니다. 따라서 종료 메서드가 호출됩니다.  

##### 웹 스코프 종류
- request: HTTP 요청 하나가 들어오고 나갈 때 까지 유지되는 스코프  
	&#10140; 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고 관리됩니다.  
- session: HTTP Session과 동일한 생명주기를 가지는 스코프  
- application: 서블릿 컨텍스트( ServletContext )와 동일한 생명주기를 가지는 스코프  
- websocket: 웹 소켓과 동일한 생명주기를 가지는 스코프  

#### request 스코프
![spring img](/assets/spring/28.JPG)  
