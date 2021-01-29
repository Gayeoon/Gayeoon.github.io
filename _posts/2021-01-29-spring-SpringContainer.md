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

```
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

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
- 빈 이름은 메서드 이름을 사용한다.  
- 빈 이름을 직접 부여할 수 도 있다.  
	- @Bean(name="gayeonService")  
&#128226; 빈 이름은 항상 다른 이름을 부여해야 합니다. 같은 이름을 부여하면, 다른 빈이 무시되거나, 기존 빈을 덮어버리거나 설정에 따라 오류가 발생합니다.  

### &#128204; 빈 조회하기

#### 모든 빈 출력하기

```
void findAllBean() {
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();
    for (String beanDefinitionName : beanDefinitionNames) {
        Object bean = ac.getBean(beanDefinitionName);
        System.out.println("name=" + beanDefinitionName + " object=" + bean);
    }
}
```

- 실행하면 스프링에 등록된 모든 빈 정보를 출력할 수 있습니다.  
- ac.getBeanDefinitionNames() : 스프링에 등록된 모든 빈 이름을 조회합니다.  
- ac.getBean() : 빈 이름으로 빈 객체(인스턴스)를 조회합니다.  

#### 애플리케이션 빈 출력하기

```
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
```

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
	
```
MemberService memberService = ac.getBean("memberService", MemberService.class);
assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
```

#### 타입으로 빈 조회하기

```
MemberService memberService = ac.getBean(MemberService.class);
assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
```

#### 구체적인 타입으로 빈 조회하기

```
MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
```

- 타입으로 조회시 같은 타입이 둘 이상 있으면 중복 오류 발생  
	- NoUniqueBeanDefinitionException  
	&#128226; 타입이 여러개일 경우 빈 이름을 지정하자!  