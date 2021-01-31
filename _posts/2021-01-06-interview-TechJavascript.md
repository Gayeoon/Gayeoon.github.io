---  
layout: post  
title: "Tech Interview - JavaScript"  
subtitle: "JavaScript CS 정리"  
categories: interview
tags: Tech JAVA OS DB NETWORK JavaScript
comments: false  
---  

## Tech Interview 준비 - JavaScript

<br/>
### &#128204; 이벤트 버블링(Event Bubbling) VS 이벤트 캡쳐링(Event Capturing)
- 이벤트 버블링(Event Bubbling) : 특정 화면 요소에서 이벤트가 발생했을 때 더 상위 요소들로 전달되어 가는 특성  
- 이벤트 캡쳐링(Event Capturing) : 상위 요소에서 하위 요소로 이벤트를 전파하는 방식 (capture:  true)  
<br/>

```
<body>
	<div class="one">
		<div class="two">
			<div class="three">
			</div>
		</div>
	</div>
</body>
```

* 이벤트 버블링  

```
var divs = document.querySelectorAll('div');
divs.forEach(function(div) {
	div.addEventListener('click', logEvent);
});

function logEvent(event) {
	console.log(event.currentTarget.className);
}
```

결과 : three -> two -> one  
<br/>

* 이벤트 캡쳐링  

```
var divs = document.querySelectorAll('div');
divs.forEach(function(div) {
	div.addEventListener('click', logEvent, {
		capture: true // default 값은 false
	});
});

function logEvent(event) {
	console.log(event.currentTarget.className);
}
```

결과 : one -> two -> three  

&#10140; stopPropagation() 웹 API를 사용하면 이벤트 전파를 막을 수 있다!  

<br/>
### &#128204; event delegation
&#10140; 특정 요소 하나하나를 개별적으로 이벤트를 부여하는 것이 아니라, 하나의 부모에 이벤트를 등록하여 부모가 이벤트를 위임하는 방식  
&#10140; 동적인 요소들에 대한 처리가 수월해지고 이벤트 핸들러를 더 적게 등록해 주기 때문에 메모리를 절약할 수 있다.  
* ex) table.onclick 핸들러로 <td> 이벤트 부여  

<br/>
### &#128204; this
&#10140; JavaScript 런타임 시에 바인딩이 이루어지는 실행 컨텍스트 중 하나로, 해당 함수가 실행되는 동안에 사용할 수 있으며 함수 호출 부분에서 this가 가리키는 것이 무엇인지 확인할 수 있다.  

<br/>
### &#128204; Prototype
&#10140; 다른 객체의 원형이 되는 객체, 함수를 정의하면 Prototype Object도 같이 생성된다.  
*Java의 Class와 같은 개념*  
- constructor : 선언한 생성자 함수. new 키워드로 함수를 호출할 경우 constructor 함수를 실행하고 객체가 생성된다.  
- prototype : 생성자 함수에 정의한 모든 객체가 공유할 **원형**  
- proto : Prototype 링크. 생성자 함수에 정의해 두었던 prototype을 참조한다.  
&#10140; prototype을 사용하면 모든 객체가 공유하고 있기 때문에 한번만 만들어진다. 그렇기 때문에 **불필요한 메모리 낭비**를 방지할 수 있다.  

<br/>
### &#128204; new 연산자
- 객체지향언어 : Class로부터 작동을 복사하여 새로운 객체를 만드는 것  
- JS : 두 객체를 연결하는 것. 새로운 객체를 다른 객체와 연결하는 **간접적인 우회 방법**  
&#10140; 직접적으로 객체를 연결해주려면 Object.create()를 이용한다.  

<br/>
### &#128204; Null VS undefined
&#10140; 두 타입 모두 값이 없음을 의미한다.  
&#10140; 변수 선언시 초기값으로 undefined를 가진다. null은 값이 비어있다는 것을 나타낸 것  

<br/>
### &#128204; 익명함수(anonymous functions)
&#10140; 즉시 실행이 필요한 상황에서 사용한다.  
```
(function () {
    //logic
})();
```

<br/>
### &#128204; JavaScript Scope Chaining
&#10140; 실행되는 컨텍스트 내에서 변수를 탐색할 때 중복되는 변수가 있더라도 먼저 탐색된 변수를 우선적으로 실행시키는 방식  

<br/>
### &#128204; Event Loop
&#10140; 자바스크립트 엔진이 Call Stack과 Callback Queue의 상태를 체크하여 Call Stack이 빈 상태가 되면, Calback Queue의 첫번째 콜백을 Call Stack으로 밀어 넣는다.  

<br/>
### &#128204; 비동기 처리
&#10140; 특정 로직의 실행이 끝날 때까지 기다려주지 않고 나머지 코드를 먼저 실행하는 것  


<br/>
### &#128204; Callback VS Promise VS async/await
- Callback : 비동기 처리를 위해 만들어짐. 다른 함수에게 인자로 전달되어 어느 시점에 실행될 수 있도록 던져주는 함수.  
&#10140; 콜백지옥이라고 불리는 중첩문이 발생하면서 한계가 생김  

```
$.get('url', function(response) {
	parseValue(response, function(id) {
		auth(id, function(result) {
			display(result, function(text) {
				console.log(text);
			});
		});
	});
});
```
*콜백지옥 -> 가독성도 떨어지고 로직 변경이 어려워진다.*  

- Promise : 어떤 값이 생성 되었을 때 그 값을 대신하는 대리자. 비동기 연산이 종료된 이후에 그 결과값이나 에러를 처리할 수 있도록 처리기를 연결해주는 역할  
	* Pending(대기) : 비동기 로직이 아직 완료되지 않은 상태 `new Promise() 메소드 호출 상태`  
	* Fulfilled(이행) : 비동기 처리가 완료되어 프로비스가 결과값을 반환해준 상태 `resolve 실행 상태 -> then으로 결과 값을 받을 수 있음`  
	* Rejected(실패) : 비동기 처리가 실패하거나 오류가 발생한 상태 `reject 호출 상태 -> catch()로 결과 값을 받을 수 있음`  
&#10140; Promise 객체를 통해 성공, 실패, 오류에 따른 후속처리가 바로 가능해져서 가독성도 좋고, 비동기 에러를 처리하기도 수월하다.  

```
new Promise(function(resolve, reject){
  setTimeout(function() {
    resolve(1);
  }, 2000);
})
.then(function(result) {
  console.log(result); // 1
  return result + 10;
})
.then(function(result) {
  console.log(result); // 11
  return result + 20;
})
.then(function(result) {
  console.log(result); // 31
});
```

- Async/await : 비동기 코드를 동기식으로 표현하는 더 나은 방법.  
&#10140; await는 Promise 객체를 받아 처리하고, 만약 비동기 함수가 아닌 동기적 함수라면 리턴값을 그대로 받는다.  
&#10140; Async 함수는 Promise 객체를 통해 비동기적으로 처리된 내용을 동기적인 코드 진행 순서로 보여주는 역할  

```
async function logTodoTitle() {
  var user = await fetchUser(); // 유저 정보 호출
  if (user.id === 1) {
    var todo = await fetchTodo(); // 유저의 todo 리스트 호출
    console.log(todo.title);
  }
}
```
