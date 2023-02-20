# Chapter34 이터러블

## 이터레이션 프로토콜
순회 가능한 데이터 컬렉션(자료구조)을 만들기 위해 ECMAScript 사양에 정의하여 미리 약속한 규칙이다.  

* **이터러블 프로토콜(iterable protocol)**  
Well-known Symbol인 Symbol.iterator를 프로퍼티 키로 사용한 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속 받은 Symbol.iterator 메서드를 호출하면 이터레이터 프로토콜을 준수한 이터레이터를 반환한다.

* **이터레이터 프로토콜(iterator protocol)**  
이터러블의 Symbol.iterator 메서드를 호출하면 이터레이터 프로토콜을 준수한 이터레이터를 반환한다. 이터레이터는 next 메서드를 소유하고, 메서드를 호출하면 이터레이터 리절트 객체를 반환한다. **이터레이터는 이터러블의 요소를 탐색하기 위한 포인터 역할을 한다.**

![이터레이션 프로토콜](https://user-images.githubusercontent.com/67082984/220032951-480adebd-ba9c-48d9-be4a-42f18c8ff11d.png)

```javascript
const isIterable = v => v !== null && typeof v[Symbol.iterator] === 'function';
console.log(isIterable([]));		// true
console.log(isIterable(''));		// true
console.log(isIterable(new Map()));	// true
console.log(isIterable(new Set()));	// true
console.log(isIterable({}));		// false

const arr = [1, 2, 3];
console.log(Symbol.iterator in arr);// true
```

```javascript
const arr = [1, 2, 3];
const iter = arr[Symbol.iterator]();
console.log(iter);				// Object [Array Iterator] {}
console.log('next' in iter);	// true
```

## 이터러블과 유사 배열 객체

```javascript
const arrLike = {
	0: 1,
	1: 2,
	2: 3,
	length: 3,
};
for (const item of arrLike) console.log(item);	// TypeError: arrLike is not iterable
```

단, arguments, DOM 컬렉션(NodeList, HTMLCollection)은 유사 배열 객체이면서 이터러블이다.

