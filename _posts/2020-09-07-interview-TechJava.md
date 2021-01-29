---  
layout: post  
title: "Tech Interview - Java"  
subtitle: "Java CS 정리"  
categories: interview
tags: Tech JAVA OS DB NETWORK
comments: true  
header-img: img/review/2020-08-28-review-book-brain-lies-1.png
---  

## Tech Interview 준비 - Java

### &#128204; Java Program 실행 과정
1. 프로그램이 실행되면 JVM은 OS로부터 이 프로그램이 필요로 하는 메모리를 할당받는다.  
&#10140; JVM은 이 메모리를 용도에 따라 여러 영역으로 나누어 관리한다.  
2. Javac가 자바 소스코드를 읽어들여 자바 바이트코드(.class)로 변환시킨다.  
3. Class Loader를 통해 class 파일들을 JVM으로 로딩한다.  
4. 로딩된 class 파일들은 Execution Engine을 통해 해석된다.  
5. 해석된 바이트 코드는 Runtime Data Area에 배치되어 실질적인 수행이 이루어진다.   

<br>
### &#128204; JVM 구조
- 자바 어플리케이션을 클래스 로더를 통해 읽어들여 자바 API와 함께 실행하게 한다.  
- Java와 OS 사이에서 중개자 역할을 수행하여 Java가 OS에 구애받지 않고 재사용 가능하게 한다.  
- 메모리 관리, GC를 수행한다.  
- class의 구조 정보가 들어간다.  

<br>
### &#128204; 가비지 컬렉션
- Heap 내의 객체 중에서 가비지를 찾아내고 찾아낸 가비지를 처리해서 힙의 메모리를 회수한다.  
- 어떤 힙 영역에 할당된 객체가 유효한 참조가 있으면 Reachability, 없으면 UnReachability로 판단한다.  

<br>
### &#128204; Heap 영역
- 객체를 저장하는 가상 메모리 공간  
- 런타임시 동적으로 할당되어 사용된다.  
- new 연산자로 생성된 객체와 배열을 저장한다.  
- GC의 관리 대상에 포함된다.   

<br>
### &#128204; Overriding vs Overloading
- 오버라이딩(Overriding)  
상위 클래스에 존재하는 메소드를 하위 클래스에서 **필요에 맞게 재정의**하는 것을 의미한다.  
- 오버로딩(Overloading)  
상위 클래스의 메소드와 이름, return 값은 동일하지만, **매개변수만 다른 메소드**를 만드는 것을 의미한다.  
다양한 상황에서 메소드가 호출될 수 있도록 한다.  

<br>
### &#128204; Call by value VS Call by reference
- Call by value(값에 의한 호출)  
장점 : 복사하여 처리하기 때문에 안전하다. 원래의 값이 보존이 된다.  
단점 : 복사를 하기 때문에 메모리가 사용량이 늘어난다.  
- Call by reference(참조에 의한 호출)  
장점 : 복사하지 않고 직접 참조를 하기에 빠르다.  
단점 : 직접 참조를 하기에 원래 값이 영향을 받는다.(리스크)  

<br>
### &#128204; == VS equals()
- ==  
비교를 위한 연산자  
비교하고자 하는 대상의 **주소값**을 비교한다.  
- equals()  
메소드이며, 객체끼리 내용을 비교할 수 있다.  
비교하고자 하는 대상의 **내용 자체**를 비교한다.  

<br>
### &#128204; 추상 클래스 VS 인터페이스
- 인터페이스   
클래스가 아니며, 클래스와 관련이 없다.    
추상 메소드와 상수만을 멤버로 가진다.  
한 개의 클래스가 여러 인터페이스를 구현할 수 있다. &#10140; **다중 구현 가능**  
*JAVA 8부터는 default 메소드가 추가되었다.*  
&#10140; default 키워드가 붙은 메소드는 일반 메소드처럼 구현할 수 있으며 자식 클래스에서 오버라이딩 할 수 있다.  
- 추상 클래스  
클래스이며 클래스와 관련이 있다.  
추상 메소드 및 일반 메소드와 멤버도 포함할 수 있다.  
한 개의 클래스가 여러개의 클래스를 상속받을 수 없다. &#10140; **다중 상속 불가능**  

<br>
### &#128204; 인터페이스 사용 이유
- 인터페이스는 메소드를 통해 나오는 결과를 이미 알고 있기 때문에 다른 개발자들이 각각의 부분을 완성시킬 때까지 기다리지 않아도 돼서 개발 시간을 단축시킬 수 있다.  
- 코드의 종속성을 줄이고 유지보수성을 높이도록 해준다.  
- 클래스의 기본틀을 제공하여 개발자들에게 정형화된 개발을 하도록 표준화 할 수 있다.  

<br>
### &#128204; 객체 VS 인스턴스  
- 클래스 타입으로 선언되었을 때 **객체**라고 부르고, 그 객체가 메모리에 할당되어 실제 프로그램에서 사용될 때 **인스턴스**라고 부른다.   

<br>
### &#128204; 객체지향프로그래밍
&#10140; 객체들간의 유기적인 상호작용을 통해서 로직을 구성하는 프로그래밍 방법이다.  
- 장점
	* 코드 재사용 용이  
		+ 남이 만든 클래스를 가져와서 사용할 수 있고, 상속을 통해 확장할 수 있음  
	* 프로그램을 유연하고 변경이 용이하게 만든다.  
		+ 절차 지향 프로그래밍에서는 코드를 수정할 때 일일이 찾아서 수정해야하는 반면 객체 지향 프로그래밍에서는 수정해야 할 부분이 클래스 내부에 맴버 변수 혹은 메소드로 있기 때문에 해당 부분만 수정하면 된다.  
	* 프로그램의 개발과 보수를 간편하게 만든다.  
		+ 클래스 단위로 모듈화 시켜서 개발이 가능하므로, 대형 프로젝트 개발 시 업무 분담이 쉽다.  
	* 직관적인 코드 분석을 가능하게 한다.  
- 단점
	* 처리속도가 상대적으로 느림  
	* 객체가 많으면 용량이 커질 수 있다.  
	* 설계 시 많은 시간과 노력이 필요하다.  

<br>
### &#128204; 캡슐화
&#10140; 객체의 속성이나 메소드를 하나로 묶고, 실제 구현 내용을 외부에 감추는 것  
- 외부 객체는 객체 내부의 구조를 얻지 못하며 객체가 노출해서 제공하는 필드와 메소드만 이용할 수 있다.  
- 외부의 잘못된 사용으로 객체가 손상되는 것을 막는다.  

<br>
### &#128204; 상속
&#10140; 상위 클래스 멤버를 하위 클래스가 물려받는 것  
- 상위 클래스를 재사용해서 하위 클래스를 빨리 개발 할 수 있다.  
- 반복된 코드의 중복을 줄여준다.  
- 유지보수의 편리성을 제공해 준다.  
- 객체의 다형성을 구현할 수 있다.  

<br>
### &#128204; 다형성
&#10140; 하나의 객체가 여러가지 타입을 가질 수 있는 것  
- 상위  타입은 모든 하위 객체가 대입될 수 있으며 하위 타입은 상위 타입으로 자동 타입 변환이 된다.  
- 다형성은 객체를 부품화 시킬 수 있다.  
 
<br>
### &#128204; JAVA
- 장점  
	* 배우기 쉬운 언어이다.  
	* 객체지향 프로그래밍이기 때문에 유연하고 유지보수가 쉽다.  
	* 자바 응용프로그램은 JVM 하고만 통신하기 때문에 운영체제에 독립적이다.  
	* Garbage Collector가 자동으로 메모리를 관리해주기 때문에 프로그래머가 메모리 관리를 하지 않아도 된다.  
 
- 단점  
	* 느리고 무겁다.  
	* 예외처리를 하지 않으면 compile 자체가 안된다.  
	
- Java 12   
&#10140; switch문 확장 (->) 쓸 수 있게  
- Java 10  
&#10140; 원래 자바는 변수 타입을 명확히 입력해줘야 했는데 var 키워드를 이용해서 컴파일러가 변수 타입을 추론하게 할 수 있다.  
- Java 8 (2014년 업데이트)  
&#10140; lambda expression 추가 / stream api 추가  
- Java 7  
&#10140; diamond operator<> / switch문에 String 사용   

<br>
### &#128204; DTO DAO VO
- DAO  
데이터베이스의 데이터 접근을 위한 객체   
데이터베이스에 접근을 하기 위한 로직과 비즈니스 로직을 분리하기 위해서 사용한다.  
DB를 사용해서 데이터를 조회하거나 조작하는 기능을 전담하도록 만든 오브젝트다.  
- DTO  
계층간 데이터 교환을 위한 객체  
- VO  
DTO와 동일한 개념이지만 read only 속성을 가진다.  

<br>
### &#128204; Tree
- 트리는 그래프의 일종으로 사이클을 형성하지 않는다.  
- 두 개의 정점 사이에 한 개의 경로를 가진다.  

<br>
### &#128204; hash 충돌 해결
- 체이닝 : 충돌이 발생하면 연결 리스트로 데이터들을 연결하는 방식  
&#10140; 외부 저장공간 사용, 검색 효율 내려감 / 상대적으로 적은 메모리 사용  
- Open Addressing : 충돌이 일어나면 다른 버켓에 데이터를 삽입하는 방식  
&#10140; 선형 탐색, 제곱 탐색, 이중 해시  
&#10140; 다른 저장공간 필요 없음 / 해시 함수의 성능에 성능이 좌지우지된다.  

<br>
### &#128204; 컬렉션(Collection)
- 여러 원소들을 담을 수 있는 자료구조  
- List : 순서(인덱스)가 있는 목록 (중복 가능)  
- Set : 순서가 중요하지 않은 목록 (중복 불가능)  
- Queue : 선입선출 방식으로 저장하는 것  
- Map : Key-Value 형태로 저장되는 것  