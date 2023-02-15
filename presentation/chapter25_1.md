# Chapter25 클래스

## 클래스는 프로토타입의 문법적 설탕인가?
클래스는 함수이며 기존 프로토타입 기반 패턴을 클래스 기반 패턴처럼 사용할 수 있도록 하는 새로운 객체 생성 메커니즘이다.  

클래스는 생성자 함수와 매우 유사하게 동작하지만 다음과 같이 몇 가지 차이가 있다.

1. 클래스를 new 연산자 없이 호출하면 에러가 발생한다.
1. 상속을 지원하는 extends와 super 키워드를 제공한다.
1. 호이스팅이 발생하지 않는 것처럼 동작한다.(let, const와 동일)
1. 클래스 내의 모든 코드에는 암묵적으로 strict mode가 지정되어 실행되며 해제할 수 없다.
1. 클래스의 constructor, 프로토타입 메서드, 정적 메서드는 모두 프로퍼티 어트리뷰트 \[\[Enumerable\]\]의 값이 false다.

## 클래스 정의
클래스는 class 키워드를 사용하여 정의한다. 식별자는 생성자 함수와 마찬가지로 파스칼 케이스를 사용하는 것이 일반적이다.

```javascript
// 클래스 정의
class Person {}
```

클래스는 일급 객체이며 다음과 같은 특징을 갖는다.

* 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
* 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
* 함수의 매개변수에게 전달할 수 있다.
* 함수의 반환값으로 사용할 수 있다.

클래스 몸체에서 정의할 수 있는 메서드는 constructor(생성자), 프로토타입 메서드, 정적 메서드의 세 가지가 있다.

```javascript
class Person {
	// 생성자
	constructor(name) {
		this.name = name;
	}
	// 프로토타입 메서드
	sayHi() { console.log(`Hi ${this.name}`); }
	// 정적 메서드
	static sayHello() { console.log('Hello!'); }
}
const me = new Person('Lee');
console.log(me.name);	// Lee
me.sayHi();			// Hi Lee
Person.sayHello();	// Hello!
```

![클래스 & 생성자 함수 정의](4.png)

## 클래스 호이스팅
클래스는 함수로 평가된다.

```javascript
class Person {}
console.log(typeof Person);	// function
```

클래스 선언문으로 정의한 클래스는 함수 선언문과 같이 소스코드 평가 과정에서 함수 객체를 생성한다.  
생성된 함수 객체는 생성자 함수로서 호출할 수 있는 함수, 즉 constructor다. 생성자 함수는 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다. 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재하기 때문이다.  

클래스 호이스팅은 let, const 키워드로 선언한 변수처럼 호이스팅된다. 따라서 클래스 선언문 이전에 일시적 사각지대에 빠지기 때문에 호이스팅이 발생하지 않는 것처럼 동작한다.

## 메서드
### constructor
constructor는 인스턴스를 생성하고 초기화하기 위한 특수한 메서드다.  
constructor 내부에서 this에 추가한 프로퍼티는 인스턴스 프로퍼티가 된다.  

constructor는 클래스 내에 최대 한 개만 존재할 수 있다. 그리고 constructor는 생략할 수 있다.  
생략하면 클래스에 빈 constructor가 암묵적으로 정의되고 빈 객체를 생성한다.

```javascript
class Person {
	// 생성자
	constructor(name) {
		// 인스턴스 생성 및 초기화
		this.name = name;
	}
}
```

### 프로토타입 메서드
생성자 함수로 인스턴스를 생성하는 경우 프로토타입 메서드를 생성하기 위해서는 명시적으로 프로토타입 메서드를 추가해야 한다.

```javascript
function Person(name) {
	this.name = name;
}
Person.prototype.sayHi = function() { console.log(`Hi ${this.name}`); };
const me = new Person('Kim');
me.sayHi();	// Hi Kim
```

하지만 클래스 몸체에서 정의한 메서드는 생성자 함수와는 다르게 기본적으로 프로토타입 메서드가 된다.  
그리고 생성자 함수와 마찬가지로 클래스가 생성한 인스턴스는 프로토타입 체인의 일원이 된다.

```javascript
class Person {
	constructor(name) {
		this.name = name;
	}
	sayHi() { console.log(`Hi ${this.name}`); }
}
const me = new Person('Kim');
me.sayHi();	// Hi Kim

console.log(Object.getPrototypeOf(me) === Person.prototype);	// true
console.log(me instanceof Person);		// true
console.log(me.constructor === Person);	// true
```

![클래스 프로토타입 체인](5.png)

### 정적 메서드
정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있는 메서드를 말한다.  
생성자 함수의 정적 메서드는 명시적으로 생성자 함수에 메서드를 추가 해야 한다.

```javascript
function Person(name) {
	this.name = name;
}
Person.sayHi = function() { console.log('Hi!'); };
Person.sayHi();	// Hi!
```

클래스에서는 메서드에 static 키워드를 붙이면 정적 메서드가 된다.

```javascript
class Person {
	static sayHi() { console.log('Hi!'); }
}
Person.sayHi();	// Hi!
```

![정적 메서드](6.png)

##### 정적 메서드와 프로토타입 메서드 차이
1. 정적 메서드와 프로토타입 메서드는 자신이 속해 있는 프로토타입 체인이 다르다.
1. 정적 메서드는 클래스로 호출하고 프로토타입 메서드는 인스턴스로 호출한다.
1. 정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.

## 클래스의 인스턴스 생성 과정

##### 1. 인스턴스 생성과 this 바인딩
"new 클래스"를 호출하면 constructor의 내부 코드가 실행되기에 앞서 암묵적으로 빈 객체가 생성된다. 초기화 되지 않은 이 빈 객체가 클래스가 생성한 인스턴스이고, 인스턴스의 프로토타입으로 클래스의 prototype 프로퍼티가 가리키는 객체가 설정된다.  
또한 인스턴스는 this에 바인딩된다.

##### 2. 인스턴스 초기화
constructor의 내부 코드가 실행되어 this에 바인딩되어 있는 인스턴스를 초기화한다.  
인스턴스에 프로퍼티를 추가하고 인수값으로 프로퍼티값을 초기화한다.

##### 3. 인스턴스 반환
클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.

```javascript
class Person {
	constructor(name) {
		console.log(this);	// Person {}
		console.log(Object.getPrototypeOf(this) === Person.prototype);	// true
		this.name = name;
	}
}
```