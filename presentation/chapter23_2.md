# Chapter23 실행 컨텍스트

## Chapter23.6 실행 컨텍스트의 생성과 식별자 검색 과정

##### 전역 코드 평가
1. 전역 실행 컨텍스트 생성
1. 전역 렉시컬 환경 생성
	1. 전역 환경 레코드 생성
		1. 객체 환경 레코드 생성
		1. 선언적 환경 레코드 생성
	1. this 바인딩
	1. 외부 렉시컬 환경에 대한 참조 결정

![전역 실행 컨텍스트 & 렉시컬 환경](/home/zerone/Pictures/Screenshots/1.png)

```javascript
var x = 1;
const y = 2;

function foo(a) {
	var x = 3;
	const y = 4;

	function bar(b) {
		const z = 5;
		console.log(a + b + x + y + z);
	}
	bar(10);
}
foo(20); // 42
```

### 전역 실행 컨텍스트 생성
런타임 실행 전 소스코드 평가할 때 먼저 전역 코드를 평가한다.  
비어있는 전역 실행 컨텍스트를 생성하고 실행 컨텍스트 스택에 푸시한다.  
전역 실행 컨텍스트는 실행 컨텍스트 스택의 최상위에 존재하게 되고, `실행 중인 실행 컨텍스트`가 된다.

![전역 실행 컨텍스트 생성](/home/zerone/Pictures/Screenshots/2.png)

### 전역 렉시컬 환경 생성
전역 렉시컬 환경을 생성하고 전역 실행 컨테스트에 바인딩한다.  
렉시컬 환경은 2개의 컴포넌트, **환경 레코드**와 **외부 렉시컬 환경에 대한 참조**로 구성된다.

![전역 렉시컬 환경 생성](/home/zerone/Pictures/Screenshots/3.png)

### 전역 환경 레코드 생성
전역 환경 레코드는 전역 변수를 관리하는 전역 스코프, 전역 객체의 빌트인 전역 프로퍼티와 빌트인 전역 함수, 표준 빌트인 객체를 제공한다.(브라우저는 window, Node는 global)  

ES6 이전에는 모든 전역 변수가 전역 객체의 프로퍼티가 되어 전역 객체가 전역 환경 레코드의 역할을 했지만,  
ES6의 let, const 키워드로 선언한 전역 변수는 개념적인 블록 내에 존재하게 된다.  

이로인해 구분하여 관리하기 위해 전역 스코프 역할을 하는 **전역 환경 레코드는 객체 환경 레코드와 선언적 환경 레코드로 구성**되어 있다.  

객체 환경 레코드는 기존의 전역 객체가 관리하던 var 키워드로 선언한 전역 변수와 함수 선언문으로 정의한 전역 함수, 빌트인 전역 프로퍼티와 빌트인 전역 함수, 표준 빌트인 객체를 관리한다.  

선언적 환경 레코드는 let, const 키워드로 선언한 전역 변수를 관리한다.  
두 레코드는 서로 협력하여 전역 스코프와 전역 객체를 관리한다.

### 객체 환경 레코드 생성
객체 환경 레코드는 `BindingObject`라고 부르는 객체와 전역 환경 레코드에 바인딩한다.  
전역 변수와 전역 함수는 **BindingObject**를 통해 전역 객체의 프로퍼티와 메서드가 된다.

![전역 환경 레코드의 객체 환경 레코드](/home/zerone/Pictures/Screenshots/4.png)

```javascript
var x = 1;

function foo(a) { }
```

### 선언적 환경 레코드 생성
let, const 키워드로 선언한 전역 변수는 선언적 환경 레코드에 등록되고 관리된다.

![전역 환경 레코드의 선언적 환경 레코드](/home/zerone/Pictures/Screenshots/5.png)

```javascript
const y = 2;
```

### this 바인딩
전역 환경 레코드의 **\[\[GlobalThisValue\]\] 내부 슬롯**에 this가 바인딩된다.  
일반적으로 전역 코드에서 this는 전역 객체를 가리키므로 전역 환경 레코드의 **\[\[GlobalThisValue\]\] 내부 슬롯**에는 전역 객체가 바인딩된다.  

this 바인딩은 전역 환경 레코드와 함수 환경 레코드에만 존재한다.

![this 바인딩](/home/zerone/Pictures/Screenshots/6.png)

### 외부 렉시컬 환경에 대한 참조 결정
외부 렉시컬 환경에 대한 참조는 현재 평가 중인 소스코드를 포함하는 외부 소스코드의 렉시컬 환경, 즉 상위 스코프를 가리킨다.  
이를 통해 단방향 링크드 리스트인 스코프 체인을 구현한다.  

현재 평가 중인 소스코드는 전역 코드이므로 스코프 체인의 종점이기 때문에 참조에는 null이 할당된다.

![외부 렉시컬 환경에 대한 참조](/home/zerone/Pictures/Screenshots/7.png)

### 전역 코드 실행
변수 할당문, 함수 호출문을 실행하려면 **식별자 결정을 위해 식별자를 검색할 때는 실행 중인 실행 컨텍스트에서 식별자를 검색하기 시작한다.**  
만약 실행중인 실행 컨텍스트의 렉시컬 환경에서 식별자를 검색할 수 없으면 외부 렉시컬 환경에 대한 참조가 기리키는 렉시컬 환경, 즉 상위 스코프로 이동하여 식별자를 검색한다.  

![전역 코드 실행](/home/zerone/Pictures/Screenshots/8.png)

```javascript
x = 1;
y = 2;
foo(20); // 42
```

### foo 함수 코드 평가
foo 함수가 호출되면 전역 코드의 실행을 일시 중단하고 foo 함수 내부로 코드의 제어권이 이동한다.  
그리고 함수 코드를 평가하기 시작한다.

1. 함수 실행 컨텍스트 생성
1. 함수 렉시컬 환경 생성
	1. 함수 환경 레코드 생성
	1. this 바인딩
	1. 외부 렉시컬 환경에 대한 참조 결정

![함수 실행 컨텍스트와 렉시컬 환경](/home/zerone/Pictures/Screenshots/9.png)

```javascript
function foo(a) {
	var x = 3;
	const y = 4;

	function bar(b) {}	
}
```

### 함수 실행 컨텍스트 생성
먼저 foo 함수 실행 컨텍스트를 생성하고 함수 렉시컬 환경이 완성되어 바인딩된 다음에 실행 컨텍스트 스택에 푸시된다.  
foo 함수 실행 컨텍스트는 실행 컨텍스트 스택의 최상위가 되며, 실행 중인 실행 컨텍스트가 된다.

### 함수 렉시컬 환경 생성
렉시컬 환경은 2개의 컴포넌트, 환경 레코드와 외부 렉시컬 환경에 대한 참조로 구성된다.

![함수 실행 컨텍스트와 렉시컬 환경 생성](/home/zerone/Pictures/Screenshots/10.png)

### 함수 환경 레코드 생성
함수 환경 레코드는 **매개변수**, **arguments 객체**, **함수 내부에서 선언한 지역 변수와 중첩 함수**를 등록하고 관리한다.

![함수 환경 레코드의 환경 레코드](/home/zerone/Pictures/Screenshots/11.png)

```javascript
function foo(a) {
	var x;
	const y;

	function bar(b) {}	
}
```

### this 바인딩
함수 환경 레코드의 **\[\[ThisValue\]\] 내부 슬롯**에 this가 바인딩된다. 바인딩될 객체는 함수 호출 방식에 따라 결정된다.  

![this 바인딩](/home/zerone/Pictures/Screenshots/12.png)

### 외부 렉시컬 환경에 대한 참조 결정
foo 함수 정의가 평가된 시점에 실행 중인 실행 컨텍스트의 렉시컬 환경의 참조가 할당된다.  
foo 함수 정의는 전역 코드 평가 시점에 평가된다. 이 시점의 실행 중인 실행 컨텍스트는 전역 실행 컨텍스트이므로 외부 렉시컬 환경에 대한 참조에는 전역 렉시컬 환경의 참조가 할당된다.  

`렉시컬 스코프`에서 자바스크립트는 **함수를 어디서 호출했는지가 아니라 어디에 정의했는지에 따라 상위 스코프를 결정한다**고 했다.  
그리고 함수 객체는 자신이 정의된 스코프, 즉 상위 스코프를 기억한다고 했다.  
함수의 상위 스코프를 함수 객체의 **\[\[Environment\]\] 내부 슬롯**에 저장한다.

![외부 렉시컬 환경에 대한 참조](/home/zerone/Pictures/Screenshots/13.png)

### foo 함수 코드 실행
**식별자 결정을 위해 실행 중인 실행 컨텍스트의 렉시컬 환경에서 식별자를 검색하기 시작한다.**

![foo 함수 코드의 실행](/home/zerone/Pictures/Screenshots/14.png)

```javascript
function foo(a) {
	x = 3;
	y = 4;

	bar(10);
}
```

### bar 함수 코드 평가

![bar 함수 실행 컨텍스트 & 렉시컬 환경](/home/zerone/Pictures/Screenshots/15.png)

```javascript
function bar(b) {
	const z;
}
```

### bar 함수 코드 실행

![bar 함수 실행 컨텍스트 & 렉시컬 환경](/home/zerone/Pictures/Screenshots/16.png)

```javascript
function bar(b) {
	z = 5;
	console.log(a + b + x + y + z);
}
```

### bar 함수 코드 실행 종료
bar 함수 코드의 마지막까지 실행하고 나면, 실행 컨텍스트 스택에서 bar 함수 실행 컨텍스트가 팝되어 제거되고 foo 실행 컨텍스트가 실행 중인 실행 컨텍스트가 된다.  

실행 컨텍스트가 스택에서 제거되었다고 해서 bar 함수 렉시컬 환경까지 즉시 소멸하는 것은 아니다.  
렉시컬 환경은 실행 컨텍스트에 의해 참조되기는 하지만 독립적인 객체다. 객체를 포함한 모든 값은 참조되지 않을 때 가비지 컬렉터에 의해 메모리 공간의 확보가 해제되어 소멸한다.  

![bar 함수 코드 실행 종료](/home/zerone/Pictures/Screenshots/17.png)

### foo 함수 코드 실행 종료

![foo 함수 코드 실행 종료](/home/zerone/Pictures/Screenshots/18.png)

### 전역 코드 실행 종료
foo 함수가 종료되면 더는 실행할 전역 코드가 없으므로 전역 코드의 실행이 종료되고 전역 실행 컨텍스트도 실행 컨텍스트 스택에서 팝되어 실행 컨텍스트에는 아무것도 남아있지 않게 된다.

## Chapter23.7 실행 컨텍스트와 블록 레벨 스코프
let, const 키워드로 선언한 변수는 모든 코드 블록(함수, if문, for문, while문, try/catch문 등)을 지역 스코프로 인정하는 블록 레벨 스코프를 따른다.

```javascript
let x = 1;
if (true) {
	let x = 10;
	console.log(x); // 10
}
console.log(x); // 1
```

![if 문의 코드 블록 렉시컬 환경](/home/zerone/Pictures/Screenshots/19.png)

![if 문 렉시컬 환경에서 이전 렉시컬 환경으로 변경](/home/zerone/Pictures/Screenshots/20.png)

이는 if 문뿐 아니라 블록 레벨 스코프를 생성하는 모든 블록문에 적용된다.