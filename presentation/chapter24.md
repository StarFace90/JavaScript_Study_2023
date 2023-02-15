# Chapter24 클로저

클로저는 자바스크립트 고유의 개념이 아니여서 정의가 ECMAScript 사양에 등장하지 않는다.  
MDN에서는 클로저에 대해 다음과 같이 정의하고 있다.  

**A closure is the combination of a function and the lexcial environment within which that function was declared**  
**클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.**  

핵심 키워드는 **"함수가 선언된 렉시컬 환경"**이다.  

## 렉시컬 스코프
실행 컨텍스트에서 얘기했듯이 **자바스크립트 엔진은 함수를 어디서 호출했는지가 아니라 함수를 어디에 정의했는지에 따라 상위 스코프를 결정한다. 이를 렉시컬 스코프(정적 스코프)라 한다.**  
스코프의 실체는 실행 컨텍스트의 렉시컬 환경이다. 이 렉시컬 환경은 자신의 "외부 렉시컬 환경에 대한 참조"를 통해 상위 렉시컬 환경과 연결된다. 이것이 바로 스코프 체인이다.  

즉 렉시컬 스코프를 간단히 얘기하자면, **"외부 렉시컬 환경에 대한 참조"에 저장할 참조값, 상위 스코프에 대한 참조는 함수 정의가 평가되는 시점에 함수가 정의된 위치에 결정된다.**

```javascript
const x = 1;
function foo() {
	const x = 10;
	bar();
}
function bar() {
	console.log(x);
}
foo();	// ?
bar();	// ?
```

## 함수 객체의 내부 슬롯 \[\[Environment\]\]
렉시컬 스코프가 가능하려면 상위 스코프를 기억해야 한다. 이를 위해 **함수는 자신의 내부 슬롯 \[\[Environment\]\]에 자신이 정의된 환경, 상위 스코프의 참조를 저장한다.**

![함수 객체의 내부 슬롯 Environment](https://user-images.githubusercontent.com/67082984/218998792-43d2485f-39ea-4d04-b61d-dfda91fd8170.png)

## 클로저와 렉시컬 환경
```javascript
const x = 1;
// 1번
function outer() {
	const x = 10;
	const inner = function() { console.log(x); };	// 2번
	return inner;
}
const innerFunc = outer();	// 3번
innerFunc();	// 4번 결과값: 10
```  

outer 함수를 호출(3번)하면 outer 함수는 중첩 함수 inner를 반환하고 생명 주기를 마감한다.  
return 하면서 outer 함수의 실행이 종료되고 outer 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거된다.  
outer 함수 실행 컨텍스트가 제거되었으므로 지역 변수 x 또한 생명 주기를 마감한다. 그러므로 지역 변수 x가 더는 유효하지 않게 되어 x 변수에 접근할 수 있는 방법이 달리 없어 보인다.  
그러나 위 코드 실행 결과(4번)는 지역 변수 x의 값인 10이다.  

이처럼 **외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다. 이러한 중첩 함수를 클로저**라고 부른다.

![outer 함수 렉시컬 환경 유지](https://user-images.githubusercontent.com/67082984/218998857-0cc0b045-c75c-456e-b046-97db5564bc46.png)

![클로저](https://user-images.githubusercontent.com/67082984/218998904-9c7cb8a7-2239-4814-a307-d1744f430261.png)

자바스크립트의 모든 함수는 상위 스코프를 기억하므로 이론적으로 모든 함수는 클로저다. 하지만 일반적으로 모든 함수를 클로저라고 하지는 않는다. 그에 해당하는 예시를 아래와 같다.  

```javascript
function foo() {
	const x = 1;
	const y = 2;

	function bar() {
		const z = 3;
		console.log(z);
	}
	return bar;
}
const bar = foo();
bar();
```

위와 같은 예제에서는 외부 함수의 생명 주기가 종료해도 "return bar"를 통해 중첩 함수를 계속 참조하고 있지만 중첩 함수에서 상위 스코프의 식별자를 참조하지 않기 때문에 일반적으로 클로저라고 하지 않는다.  
이처럼 상위 스코프의 어떤 식별자도 참조하지 않는 경우 대부분의 모던 브라우저는 최적화를 통해 상위 스코프를 기억하지 않는다. 참조하지도 않는 식별자를 기억하는 것은 메모리 낭비이기 때문이다.

```javascript
function foo() {
	const x = 1;

	function bar() { console.log(x); }
	bar();
}
foo();
```

다음의 위 예제에서는 bar 함수에서 x가 상위 스코프를 참조하기 때문에 클로저였지만 함수를 실행하고 곧바로 소멸하기 때문에 일반적으로 클로저라고 하지 않는다.

```javascript
function foo() {
	const x = 1;
	const y = 2;

	function bar() { console.log(x); }
	return bar;
}
const bar = foo();
bar();
```

위 예제는 bar 중첩 함수에서 상위 스코프의 식별자를 참조하고 있고 중첩 함수가 외부 함수보다 더 오래 유지되기 때문에 클로저이다.  
다만 클로저인 중첩 함수 bar는 상위 스코프의 x, y 식별자 중에서 x만 참조하고 있다. 이 경우 대부분의 모던 브라우저는 최적화를 통해 상위 스코프의 식별자 중에서 클로저가 참조하고 있는 식별자만을 기억한다.  

클로저에 의해 참조되는 상위 스코프의 변수를 **자유 변수**라고 부른다. 클로저란 "자유 변수에 묶여있는 함수"라고 할 수 있다.

## 클로저의 활용
**클로저는 상태를 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하는 목적으로 사용한다.**

```javascript
let num = 0;
const increase = function() { return ++num; };
console.log(increase());	// 1
console.log(increase());	// 2
console.log(increase());	// 3
```

```javascript
const increase = (function() {
	let num = 0;

	return function() {
		return ++num;
	};
}());
console.log(increase());	// 1
console.log(increase());	// 2
console.log(increase());	// 3
```

## 캡슐화와 정보 은닉
캡슐화는 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것을 말한다.  
캡슐화는 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 정보 은닉이라 한다.

대부분의 객체지향 프로그래밍 언어는 클래스를 정의하고 멤버에 대하여 접근 제한자를 통해 공개 범위를 한정할 수 있다.  
하지만 자바스크립트는 접근 제한자를 제공하지 않는다. 따라서 모든 프로퍼티와 메서드는 기본적으로 외부에 공개되어 있다.  

```javascript
function Person(name, age) {
	this.name = name;
	let _age = age;

	this.sayHi = function() { console.log(`${this.name}. ${_age}.`); };
}
const me = new Person('Lee', 20);
me.sayHi();				// Lee. 20.
console.log(me.name);	// Lee
console.log(me._age);	// undefined
```

위 예제처럼 "\_age" 변수가 마치 private 접근 제한자인 것처럼 구현은 가능하다. 이처럼 함수의 지역 변수, 클로저 등으로 private와 유사하게 정보 은닉이 가능한 것처럼 보이지만 자바스크립트는 정보 은닉을 완전하게 지원하지 않는다.

## 클로저 실무 사례
```html
<!DOCTYPE html>
<html>
  <body>
  <button id="plus">+</button>
  <p id="count">0</p>
  <script>
    let plusBtn = document.getElementById('plus');
    let countTxt = document.getElementById('count');

    // count라는 변수를 만약 전역 변수로 선언되어
    // 카운팅되게 만들었다면 굉장히 위험한 프로그램이 될 것이다.
    let plus = (function () {
      // 카운트 상태를 유지하기 위한 자유 변수
      let count = 0;
      // 클로저를 반환
      return function () {
        return count++;
      };
    }());

    plusBtn.onclick = function () {
      countTxt.innerHTML = plus();
    };
  </script>
</body>
</html>
```
