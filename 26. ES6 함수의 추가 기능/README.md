# 26장 ES6 함수의 추가 기능

## 26.1 함수의 구분

ES6 이전의 모든 함수는 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수 있다.

⇒ callable(호출할 수 있는 객체) 이면서 constructor(인스턴스를 생성할 수 있는 객체)다.

| ES6 함수의 구분    | constructor | prototype | super | arguments |
| ------------------ | ----------- | --------- | ----- | --------- |
| 일반 함수 (Normal) | O           | O         | X     | O         |
| 메서드 (Method)    | X           | X         | O     | O         |
| 화살표 함수(Arrow) | X           | X         | X     | X         |

## 26.2 메서드

✅ ES6에서 메서드는 메서드 축약 표현으로 정의된 함수이며 인스턴스를 생성할 수 없는 non-constructor다.

```jsx
const obj = {
  x: 1,
  // foo는 메서드
  foo() {
    return this.x;
  },
  // bar에 바인딩된 함수는 메서드가 아닌 일반 함수다.
  bar: function () {
    return this.x;
  },
};

console.log(obj.foo()); // 1
console.log(obj.bar()); // 1

new obj.foo(); // typeError: obj.foo is not a constructor
new obj.bar(); // bar {}

// constructor가 아닌 ES6 메서드 이기 때문에 prototype 프로퍼티가 없다.
obj.foo.hasOwnProperty("prototype"); // false

// consturctor인 일반 함수이므로 prototype 프로퍼티가 있다.
obj.bar.hasOwnProperty("prototype"); // true
```

✅ 자신을 바인딩한 객체를 가리키는 내부 슬롯 [[HomeObject]] 를 갖는다.
[[HomeObject]]을 가져야만 super 키워드를 사용할 수 있다.

```jsx
const derived = {
  __proto__: base,

  // super는 sayHi의 [[HomeObject]]의 프로토타입인 base를 가리킨다.
  sayHi() {
    return `${super.sayHi()}. how are you doing?`;
  },
};
```

## 26.3 화살표 함수

화살표 함수는 콜백 함수로서 정의할 때 유용하다.

✅ 화살표 함수와 일반 함수의 차이

1. 화살표 함수는 인스턴스를 생성할 수 없는 non-constructor다.
2. 중복된 매개변수 이름을 선언할 수 없다.
3. 함수 자체의 this, arguments, super, [new.target](http://new.target) 바인딩을 갖지 않는다.

   스코프 체인을 통해 상위 스코프의 this, arguments, super, [new.target](http://new.target) 을 참조한다.

✅ this

화살표 함수는 함수 자체의 this 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 this를 참조하면 상위 스코프의 this를 그대로 참조한다. ⇒ lexical this

프로퍼티에 할당한 화살표 함수도 스코프 체인 상에서 가장 가까운 상위 함수 중에서 화살표 함수가 아닌 함수의 this를 참조한다.

```jsx
// 화살표 함수의 상위 스코프는 전역
// increase 프로퍼티에 할당한 화살표 함수의 this는 전역 객체

const counter = {
  num: 1,
  increase: () => ++this.num,
};

console.log(counter.increase()); // NaN
```

✅ super

함수 자체의 super 바인딩을 갖지 않는다.

화살표 함수 내부에서 super를 참조하면 this와 마찬가지로 상위 스코프의 super를 참조한다.

✅ arguments

함수 자체의 arguments 바인딩을 갖지 않는다.

화살표 함수 내부에서 arguments 를 참조하면 this와 마찬가지로 상위 스코프의 arguments 를 참조한다.

## 26.4 Rest 파라미터

✅ 기본 문법

- Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받는다.
- Rest 파라미터는 단 하나만 선언할 수 있다.
- Rest 파라미터는 함수 객체의 length 프로퍼티에 영향을 주지 않는다.

✅ Rest 파라미터와 arguments 객체

화살표 함수로 가변 인자 함수를 구현할 때는 반드시 Rest 파라미터를 사용해야 한다.

```jsx
function sum(...args) {
  return args.reduce((pre, cur) => pre + cur, 0);
}

console.log(sum(1, 2, 3, 4, 5));
```

## 26.5 매개변수 기본값

✅ 인수가 전달되지 않은 매개변수의 값은 undefined이다.

✅ ES6에서 도입된 매개변수 기본값을 사용하면 함수 내에서 수행하던 인수 체크 및 초기화를 간소화할 수 있다. (기본값의 경우 인수를 전달하지 않은 경우와 undefined를 전달한 경우에만 유효)

```jsx
function sum(x = 0, y = 0) {
  return x + y;
}

console.log(sum(1, 2)); // 3
console.log(sum(1)); // 1
```

✅ Rest 파라미터에는 기본값을 지정할 수 없다.

✅ 매개변수의 기본값은 함수 정의시 선언한 매개변수 개수를 나타내는 함수 객체의 length 프로퍼티와 arguments 객체에 아무런 영향을 주지 않는다.
