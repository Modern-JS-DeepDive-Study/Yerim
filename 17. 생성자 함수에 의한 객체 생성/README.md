# 17. 생성자 함수에 의한 객체 생성

## 17.1 Object 생성자 함수

생성자 함수란 new 연산자와 함께 호출하여 객체를 생성하는 함수를 의미

생성자 함수에 의해서 생성된 객체를 인스턴스라 함

```jsx
// String 생성자 함수에 의해 String 객체 저장
const strObj = new String("Lee");
console.log(typeof strObj); // object
```

✔️ 그닥 유용하진 않음

## 17.2 생성자 함수

### ✅ 객체 리터럴에 의한 객체 생성 방식의 문제점

직관적이고 간편하지만 단 하나의 객체만 생성

```jsx
const circle1 = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  },
};

const circle2 = {
  radius: 10,
  getDiameter() {
    return 2 * this.radius;
  },
};
```

✔️ 프로퍼티 구조가 동일함에도 불구하고 매번 같은 프로퍼티와 메서드를 기술해야 함

### ✅ 생성자 함수에 의한 객체 생성 방식의 장점

일반 함수와 동일한 방법으로 생성자 함수를 정의하고 **new연산자와 함께 호출하면 해당 함수는 생성자 함수로 동작** (함께 호출하지 않으면 일반 함수로 동작)

```jsx
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 생성자 함수로 동작하지 않음
const circle = Circle(15);
```

### ✅ 생성자 함수의 인스턴스 생성 과정

**필요한 것 : 인스턴스 생성, 생성된 인스턴스를 초기화(인스턴스 프로퍼티 추가 및 초기값 할당)**

1. 인스턴스 생성과 this 바인딩

   런타임 이전에 바인딩이 일어나며, 암묵적으로 생성된 빈 객체(인스턴스)는 this에 바인딩

2. 인스턴스 초기화

   this에 바인딩 되어있는 인스턴스를 초기화

3. 인스턴스 반환

   생성자 함수 내부에서 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this를 암묵적으로 반환

   ✔️ this가 아닌 다른 객체를 명시적으로 반환하면 this가 반환되지 못하고 return 문에 명시된 객체가 반환

   ✔️ 명시적으로 원시 값을 반환하면 원시 값 반환은 무시되고 암묵적으로 this가 반환

   ⇒ 생성자 함수의 기본 동작을 훼손하기 때문에 반드시 생략

```jsx
function Circle(radius) {
  // 1. 암묵적으로 빈 객체가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3. 암묵적으로 this를 반환
  // 명시적으로 객체를 반환하면 this반환이 무시
  return {};

  // 명시적으로 원시 값 반환은 무시되고 암묵적으로 this가 반환
  return 100;
}
```

### ✅ 내부 메서드 [[Call]] 과 [[Construct]]

✔️ 일반 객체는 호출할 수 없지만 함수는 호출할 수 있다.

✔️ 함수가 일반 함수로서 호출되면 함수 객체의 내부 메서드 `[[Call]]` 이 호출되고 new 연산자와 함께 생성자 함수로서 호출되면 내부 메서드 `[[Construct]]` 가 호출

```jsx
function foo() {}

// 일반적인 함수로서 호출: [[call]]
foo();

// 생성자 함수로서 호출: [[constructor]]
new foo();
```

✔️ callable은 호출할 수 있는 객체, 즉 함수

✔️ constructor는 생성자 함수로서 호출할 수 있는 함수

✔️ non-constructor는 객체를 생성자 함수로서 호출할 수 없는 함수

⭕️ 함수 객체는 callable 이면서 constructor이거나, callable이면서 non-constructor이다.

### ✅ Constructor와 Non-constructor의 구분

✔️ constructor: 함수 선언문, 함수 표현식

✔️ non-constructor: 화살표 함수, 메서드 축약 표현

### ✅ new 연산자

new 연산자와 함께 함수를 호출하면 해당 함수는 생성자 함수로 동작

```jsx
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new연산자 없이 생성자 함수 호출하면 일반 함수로서 호출
const circle = Circle(5);
console.log(circle); // undefined

// 일반 함수 내부의 this는 전역 객체 window를 가리킴
console.log(radius);
console.log(getDiameter()); // 10

circle.getDiameter(); // typeError
```

### ✅ new.target

new 연산자와 함께 생성자 함수로서 호출되면 함수 내부의 new.target은 함수 자신을 가리킨다.

new 연산자 없이 일반 함수로서 호출된 함수 내부의 new.target은 undefined다.
