# Chapter41 타이머

### 호출 스케줄링
함수를 명시적으로 호출하지 않고 일정 시간이 경과된 이후에 호출되도록 하는 것을 호출 스케줄링이라 한다.  
함수 호출을 예약하려면 타이머 함수를 사용할 수 있다. 자바스크립트에서는 setTimeout, setInterval 타이머 함수를 제공한다.  

타이머 함수는 ECMAScript 사양에 정의된 빌트인 함수가 아니고 브라우저, Node.js 환경에서 모두 전역 객체의 메서드로서 호스트 객체다.  

setTimeout 함수는 단 한 번 동작하는 타이머를 생성하는 함수이고, clearTimeout 함수는 setTimeout 함수가 반환한 타이머 id를 clearTimeout 함수의 인수로 전달하여 타이머를 취소할 수 있다.

setInterval 함수는 반복 동작하는 타이머를 생성하는 함수이고, clearInterval 함수는 clearTimeout과 마찬가지로 타이머를 취소할 수 있다.

### 디바운스와 스로틀
scroll, resize, input, mousemove 같은 이벤트는 짧은 시간 간격으로 연속해서 발생한다. 이러한 이벤트에 바인딩한 이벤트 핸들러는 과도하게 호출되어 성능에 문제를 일으킬 수 있다.  

디바운스와 스로틀은 짧은 시간 간격으로 연속해서 발생하는 이벤트를 **그룹화 해서 과도한 이벤트 핸들러의 호출을 방지하는 프로그래밍 기법이다.**

##### 디바운스(debounce)
디바운스는 짧은 시간 간격으로 발생하는 이벤트를 그룹화해서 **마지막에 한 번만** 이벤트 핸들러가 호출되도록 한다.  
즉, 마지막 이벤트 핸들러가 호출되고 나서 지정한 경과시간이 지나야 진행된다.  
디바운스는 resize 이벤트 처리나 input 요소에 입력된 값으로 ajax 요청하는 입력 필드 자동완성 UI 구현, 버튼 중복 클릭 방지 처리 등에 유용하게 사용된다.

```javascript
const $input = document.querSelector('input');

const debounce = (cb, delay) => {
	let timerId;
	return (...args) => {
		if (timerId) clearTimeout(timerId);
		timerId = setTimeout(cb, delay, ...args);
	};
};

$input.oninput = debounce(e => {
	// code
}, 500);
```

![debounce](1.png)

실무에서는 Underscore의 debounce 함수나 Lodash의 debounce 함수를 사용하는 것을 권장한다.

##### 스로틀(throttle)
스로틀은 짧은 시간 간격으로 연속해서 발생하는 이벤트를 그룹화해서 **일정 시간 단위로** 이벤트 핸들러가 호출되도록 호출 주기를 만든다. 
즉, 이벤트가 계속 발생해도 지정한 경과시간이 지났을 때 진행이 되고, 또 경과시간이 지나야만 발생한 이벤트가 진행된다.  
스로틀은 scroll 이벤트 처리나 무한 스크롤 UI 구현 등에 유용하게 사용된다.

```javascript
const $input = document.querSelector('input');

const throttle = (cb, delay) => {
	let timerId;
	return (...args) => {
		if (timerId) return;
		timerId = setTimeout(() => {
			cb(...args);
			timerId = null;
		}, delay);
	};
};

$input.oninput = throttle(e => {
	// code
}, 500);
```

![debounce](2.png)

실무에서는 Underscore의 throttle 함수나 Lodash의 throttle 함수를 사용하는 것을 권장한다.