## 18.1 일급 객체

### ✅ 일급 객체란

1. 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능
2. 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
3. 함수의 매개변수에 전달할 수 있다.
4. 함수의 반환값으로 사용할 수 있다.

```jsx
// 1. 함수는 무명의 리터럴로 생성할 수 있다.
// 2. 함수는 변수에 저장할 수 있다.
const increase = function (num) {
  return ++num;
};

const decrease = function (num) {
  return --num;
};

// 2. 함수는 객체에 저장할 수 있다.
const auxs = { increase, decrease };

// 3. 함수의 매개변수에 전달할 수 있다.
// 4. 함수의 반환값으로 사용할 수 있다.

function makeCounter(aux) {
  let num = 0;

  return function () {
    num = aux(num);
    return num;
  };
}
```

✔️ 함수를 객체와 동일하게 사용할 수 있으며, 리터럴로 정의해 런타임에 함수 객체로 정의 됨

✔️ 일반 객체는 호출할 수 없지만 함수 객체는 호출할 수 있다.

## 18.2 함수 객체의 프로퍼티

### ✅ arguments 프로퍼티

함수 호출 시 전달된 인수들의 정보를 담고 있는 순회 가능한(iterable) 유사 배열 객체이며, 함수 내부에서 지역 변수처럼 사용 ⇒ 함수 외부에서는 참조할 수 없음

✔️ 전달되지 않은 인수는 undefined로 초기화된 상태를 유지, 더 많이 전달한 경우 초과된 인수는 무시

✔️ arguments 객체는 매개변수 개수를 확정할 수 없는 가변 인자 함수를 구현할 때 유용

```jsx
function sum(...args) {
  return args.reduce((pre, cur) => pre + cur, 0);
}
```

### ✅ **proto** 접근자 프로퍼티

`__proto__` 프로퍼티는 [[Prototype]] 내부 슬롯이 가리키는 프로토타입 객체에 접근하기 위해 사용하는 접근자 프로퍼티다.

### ✅ prototype 프로퍼티

생성자 함수로 호출할 수 있는 객체, 즉 constructor 만이 소유하는 프로퍼티

```jsx
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}).hasOwnProperty("prototype"); // true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}).hasOwnProperty("prototype"); //false
```
