# Chapter 19 프로토타입

### 상속과 프로토타입
* 상속은 객체지향 프로그래밍의 핵심 개념으로 어떤 객체의 프로퍼티, 메서드를 다른 객체가 상속받아 그대로 사용할 수 있는 것을 말한다.
* 자바스크립트는 프로토타입을 기반으로 상속을 구현하여 불필요한 중복을 제거한다.

##### 메서드 중복
![생성자 함수](/home/zerone/Pictures/Screenshots/1.png)  

##### 상속으로 메서드 중복 제거
![생성자 함수 상속](/home/zerone/Pictures/Screenshots/2.png)

```javascript
// prototype 상속 예제
// 생성자 함수
function Person(name) {
	this.name = name;
}
Person.prototype.sayHello = function() {
	console.log(`Hi! Hello World ${this.name}`);
};

// 인스턴스 생성
const person1 = new Person('Lee');
const person2 = new Person('Kim');
console.log(person1.sayHello === person2.sayHello); // true
console.log(person1.sayHello());	// Hi! Hello World Lee
console.log(person2.sayHello());	// Hi! Hello World Kim
```
생성자 함수가 생성한 모든 인스턴스는 자신의 프로토타입, 부모 객체 역할을 하는 prototype의 모든 프로퍼티, 메서드를 상속받는다.  

우리는 이미 상속을 받아서 사용하고 있는 것을 알고있다.  
Object 객체의 hasOwnProperty, toString, valueOf 등의 메서드를 상속받아 사용하고 있었다.
```javascript
const obj = {};
obj.toString();
obj.valueOf();
```

### 프로토타입 객체
모든 객체는 **\[\[Prototype\]\]**이라는 내부 슬롯을 가지며, 이 내부 슬롯의 값은 프로토타입의 참조다.(null인 경우도 있다)  
객체가 생성될때 객체 생성 방식에 따라 프로토타입이 결정되고 **\[\[Prototype\]\]**에 저장된다.  

모든 객체는 하나의 프로토타입을 갖고 있고, 모든 프로토타입은 생성자 함수와 연결되어 있다.
##### 객체와 프로토타입과 생성자 함수는 서로 연결되어 있다
![연결 구조](/home/zerone/Pictures/Screenshots/3.png)

**\[\[Prototype\]\]** 내부 슬롯에는 직접 접근할 수 없지만, **\_\_proto\_\_** 접근자 프로퍼티를 통해 자신의 프로토타입에 간접적으로 접근할 수 있다.  
하지만 **\_\_proto\_\_** 접근자 프로퍼티를 코드 내에서 직접 사용하는 것은 권하지 않는다고 한다.  
그 이유는 모든 객체가 **\_\_proto\_\_** 접근자 프로퍼티를 사용할 수 있는 것은 아니기 때문이다.(직접 상속으로 인해)
```javascript
// __proto__ 접근자 프로퍼티 대신 프로토타입의 참조를 취득 및 변경
{}.__proto__;						// X
{}.__proto__ = obj;					// X

Object.getPrototypeOf(obj);			// O
Object.setPrototypeOf(obj, parent);	// O
```

### prototype 프로퍼티
prototype 프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다.  
그래서 non-constructor인 화살표 함수, ES6 메서드 축약 표현으로 정의한 메서드는 prototype 프로퍼티를 소유하지 않으며 프로토타입도 생성하지 않는다.
```javascript
const Person = name => {
	this.name = name;
};
Person.hasOwnProperty('prototype');	// false
Person.prototype;	// false

const obj = {
	foo() {}
};
obj.foo.hasOwnProperty('prototype'); //false
obj.foo.prototype;	// false
```

객체 리터럴에 의해 생성된 객체의 프로토타입은 Object.prototype이고 생성자 함수에 의해 생성된 객체의 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체다.  
프로토타입과 생성자 함수는 단독으로 존재할 수 없고 항상 쌍으로 존재해야 한다.

### 프로토타입의 생성 시점
함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 생성된다.
```javascript
Person.prototype;	// { constructor: f}

function Person(name) {
	this.name = name;
}
```
또한 일반 함수 이외에도 빌트인 생성자 함수도 마찬가지로 전역 객체가 생성되는 시점에 생성된다.

### 프로토타입 체인
자바스크립트는 객체의 프로퍼티, 메서드에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티, 메서드가 없다면 **\[\[Prototype\]\]** 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다.  
이를 **프로토타입 체인**이라 한다.

##### 프로토타입 체인
![프로토타입 체인](/home/zerone/Pictures/Screenshots/4.png)
```javascript
function Person(name) {
	this.name = name;
}
Person.prototype.sayHello = function() {}

const me = new Person('Lee');
me.hasOwnProperty('name');
```
프로토타입 체인은 상속과 프로퍼티, 메서드 검색을 위한 매커니즘이다.
