# 25. 클래스

## 25.1 클래스는 프로토타입의 문법적 설탕인가?

✅ 클래스를 프로토타입 기반 객체 생성 패턴의 단순한 문법적 설탕보다는 **새로운 객체 생성 메커니즘**으로 보는 것이 합당

✅ **클래스와 생성자 함수의 차이**

1. 클래스를 new연산자 없이 호출하면 에러가 발생한다. 하지만 생성자 함수를 new 연산자 없이 호출하면 일반 함수로서 호출된다.
2. 클래스는 상속을 지원하는 extends와 super 키워드를 제공한다. 하지만 생성자 함수는 extends 와 super 키워드를 지원하지 않는다.
3. 클래스는 호이스팅이 발생하지 않는 것처럼 동작한다.

## 25.2 클래스 정의

클래스는 익명 클래스로 표현할 수도 있고, 기명 클래스로 표현할 수도 있다.

**클래스는 일급 객체이다.**

```js
const Person = class {};
const Person = class MyClass {};
```

```js
const Person {
    // 생성자
    constructor(name) {
        // 인스턴스 생성 및 초기화
        this.name = name;
    }

    // 프로토타입 메서드
    sayHi() {
        console.log(`Hi! My name is ${this.name}`);
    }

    // 정적 메서드
    static sayHello() {
        console.log(`Hello!`);
    }
}

// 인스턴스 생성
const me = new Person('Lee');

console.log(me.name); // Lee
me.sayHi(); // Hi! My name is Lee
Person.sayHello(); // Hello!
```

## 25.3 클래스 호이스팅

클래스는 **함수**로 평가

단, 클래스는 클래스 정의 이전에 참조할 수 없다.

```js
class Person {}

console.log(typeof Person); // function
```

생성자 함수로서 호출할 수 있는 함수는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성

클래스 선언문도 변수 선언, 함수 정의와 마찬가지로 호이스팅 발생 ⇒ 일시적 사각지대에 빠져 호이스팅이 발생하지 않는 것처럼 동작

## 25.4 인스턴스 생성

클래스는 생성자 함수이며 new 연산자와 함께 호출되어 인스턴스를 생성

```js
const Person = class MyClass {};

// 함수 표현식과 마찬가지로 클래스를 가리키는 식별자로 인스턴스를 생성해야 한다.
const me = new Person();

// 클래스 이름 MyClass는 함수와 동일하게 클래스 몸체 내부에서만 유효한 식별자
console.log(MyClass); // ReferenceError: MyClass is not defined

const you = new MyClass(); // ReferenceError: MyClass is not defined
```

## 25.5 메서드

1. constructor(생성자)
2. 프로토타입 메서드
3. 정적 메서드

**✅ constructor**

인스턴스를 생성하고 초기화하기 위한 특수한 메서드

```js
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }
}
```

⇒ constructor 내부에서 this에 추가한 프로퍼티는 인스턴스 프로퍼티가 되며, constructor 내부의 this는 생성자 함수와 마찬가지로 클래스가 생성한 인스턴스를 가리킨다.

✨ constructor내에서는 인스턴스의 생성과 동시에 인스턴스 프로퍼티 추가를 통해 인스턴스의 초기화를 실행한다. ⇒ 인스턴스를 초기화하려면 constructror를 생략해서는 안된다.

✨ constructor 내부에서 명시적으로 this가 아닌 다른 값을 반환하는 것은 클래스의 기본 동작을 훼손하기 때문에 **반드시 return문을 생략**해야 한다.

```js
class Person {
	// 생성자
	constructor(name) {
		// 인스턴스 생성 및 초기화
		this.name = name;

		// 명시적으로 객체를 반환하면 this 반환이 무시된다.
		return {};
	}
}

// constructor에서 명시적으로 반환한 빈 객체가 반환
const me = new Person('Lee');
console.log(me); // {}

-----------------------------------------------------------------
class Person {
	// 생성자
	constructor(name) {
		// 인스턴스 생성 및 초기화
		this.name = name;

		// 명시적으로 원시값을 반환하면 원시값 반환은 무시되고 암묵적으로 this가 반환된다.
		return 100;
	}
}

const me = new Person('Lee');
console.log(me); // Person {name : "Lee"}
```

**✅ 프로토타입 메서드**

생성자 함수와 마찬가지로 클래스가 생성한 인스턴스는 프로토타입 체인의 일원이 된다.

**✅ 정적 메서드**

메서드에 static 키워드를 붙이면 정적 메서드가 된다.

정적 메서드는 클래스에 바인딩된 메서드

**정적 메서드는 프로토타입 메서드처럼 인스턴스로 호출하지 않고 클래스로 호출하기 때문에 인스턴스로 호출할 수 없다.**

```jsx
const me = new Person("Lee");
me.sayHi(); // TypeError: me.sayHi is not a function
```

**✅ 정적 메서드와 프로토타입 메서드의 차이**

1. 정적 메서드와 프로토타입 메서드는 자신이 속해 있는 프로토타입 체인이 다르다.
2. 정적 메서드는 클래스로 호출하고 프로토타입 메서드는 인스턴스로 호출한다.
3. 정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.

**✅ 클래스에서 정의한 메서드 특징**

1. function 키워드를 생략한 메서드 축약 표현을 사용한다.
2. 객체 리터럴과는 다르게 클래스에 메서드를 정의할 때는 콤마가 필요 없다.
3. 암묵적으로 strict mode로 실행된다.
4. `for… in` 문이나 `object.keys` 메서드 등으로 열거할 수 없다. 즉, 프로퍼티의 열거 가능 여부를 나타내며, 불리언 값을 갖는 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 false다.
5. 내부 메서드 `[[Construct]]` 를 갖지 않는 non-constructor다. 따라서 new 연산자와 함께 호출할 수 없다.

## 25.6 클래스의 인스턴스 생성 과정

### 1. 인스턴스 생성과 this 바인딩

### 2. 인스턴스 초기화

### 3. 인스턴스 반환

```jsx
class Person {
  constructor(name) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // Person{}
    console.log(Object.getPrototypeOf(this) === Person.prototype); // true

    // 2. this에 바인딩 되어있는 인스턴스 초기화
    this.name = name;
    // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환
  }
}
```

## 25.7 프로퍼티

**✅ 인스턴스 프로퍼티**

```jsx
class Person {
  constructor(name) {
    // 인스턴스 프로퍼티
    this.name = name;
  }
}
```

**✅ 접근자 프로퍼티**

접근자 프로퍼티는 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티

```jsx
const person = {
    firstName: "Ungmo",
    lastName: "Lee",

    // fullName은 접근자 함수로 구성된 접근자 프로퍼티
    // getter
    get fullName() {
        return `${this.firstName} ${this.lastName}`;
    }

    // setter
    set fullName(name) {
        [this.firstName, this.lastName] = name.split(" ");
    }
}
```

**✅ 클래스 필드 정의 제안**

클래스 필드를 참조하는 경우 자바스크립트에서는 this를 반드시 사용해야 하며, 클래스 필드에 초기값을 할당하지 않으면 undefined를 갖는다.

함수는 일급 객체이므로 함수를 클래스 필드에 할당할 수 있다. ⇒ 클래스 필드를 통해 메서드를 정의할 수도 있다.

```js
const person = {

    // 클래스 필드에 문자열을 할당
    name = "Lee";

    // 클래스 필드에 함수를 할당
    getName = function () {
        return this.name;
    }
}
```

✨ 클래스 필드에 함수를 할당하는 경우, 프로토타입 메서드가 아닌 인스턴스 메서드가 된다. 그렇기 때문에 클래스 필드에 함수를 할당하는 것을 권장하지 않는다.

**✅ private 필드 정의 제안**

인스턴스 프로퍼티는 인스턴스를 통해 클래스 외부에서 언제나 참조할 수 있다. = `public` 이다.

private의 필드의 선두에 `#` 을 붙인다.

```js
class Person {
  // private 필드 정의
  #name = "";

  constructor(name) {
    // private 필드 참조
    this.#name = name;
  }
}

const me = new Person("Lee");

// private 필드 #name은 클래스 외부에서 참조할 수 없다.
console.log(me.#name);
```

| 접근 가능성                 | public | private |
| --------------------------- | ------ | ------- |
| 클래스 내부                 | o      | o       |
| 자식 클래스 내부            | o      | x       |
| 클래스 인스턴스를 통한 접근 | o      | x       |

**✅ static필드 정의 제안**

```jsx
class MyMath {
  // static public 필드 정의
  static PI = 22 / 7;

  // static private 필드 정의
  static #num = 10;

  // static 메서드
  static increment() {
    return ++MyMath.#num;
  }
}
```

## 25.8 상속에 의한 클래스 확장

**✅ 클래스 상속과 생성자 함수 상속**

상속에 의한 클래스 확장은 기존 클래스를 상속받아 새로운 클래스를 확장하여 정의하는 것

**✅ extends 키워드**

상속을 통해 클래스를 확장하려면 extends 키워드를 사용하여 상속받을 클래스를 정의

```jsx
// 수퍼(베이스/부모) 클래스
class Base {}

// 서브 (파생/자식) 클래스
class Derived extends Base {}
```

extends 키워드는 수퍼클래스와 서브클래스 간의 상속 관계를 결정하는 것

**✅ 동적 상속**

클래스뿐만 아니라 생성자 함수를 상속받아 클래스를 확장할 수 있다. 단, extends 키워드 앞에는 반드시 클래스가 와야 한다.

```jsx
// 생성자 함수
function Base(a) {
  this.a = a;
}

// 생성자 함수를 상속받는 서브클래스
class Derived extends Base {}

const derived = new Derived(1);
console.log(derived); // Derived {a:1}
```

동적으로 상속받을 대상을 결정할 수 있다.

```jsx
function Base1() {}

class Base2 {}

let condition = true;
// 조건에 따라 동적으로 상속 대상을 결정하는 서브 클래스
class Derived extends (condition ? Base1 : Base2) {}

const derived = new Derived();

console.log(derived instanceof Base1); // true
console.log(derived instanceof Base2); // false
```

**✅ 서브클래스의 constructor**

클래스에서 constructor를 생략하면 클래스에 비어있는 constructor가 암묵적으로 정의

```jsx
constructor(...args){ suber(...args); }
```

**✅ super 키워드**

**✨ super 호출**

super를 호출하면 수퍼클래스의 constructor(super-constructor)를 호출한다.

수퍼 클래스에서 추가한 프로퍼티와 서브클래스에서 추가한 프로퍼티를 갖는 인스턴스를 생성한다면 서브클래스의 constructor를 생략할 수 없다.

🚧 주의할 사항

1. 서브클래스에서 constructor를 생략하지 않는 경우 서브 클래스의 constructor에서는 반드시 super를 호출해야 한다.
2. 서브클래스의 constructor에서 super를 호출하기 전에는 this를 참조할 수 없다.
3. super는 반드시 서브클래스의 constructor에서만 호출한다. 서브클래스가 아닌 클래스의 constructor나 함수에서 super를 호출하면 에러가 발생한다.

**✨ super 참조**

1. 메서드 내에서 super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

   super 참조가 동작하기 위해서는 super를 참조하고 있는 메서드가 바인딩되어 있는 객체의 프로토타입을 찾을 수 있어야 한다.

2. 서브클래스의 정적 메서드 내에서 super.sayHi는 수퍼클래스의 정적 메서드 sayHi를 가리킨다.

**✅ 상속 클래스의 인스턴스 생성 과정**

1. 서브클래스의 super 호출

   서브클래스는 자신이 직접 인스턴스를 생성하지 않고 수퍼클래스에게 인스턴스 생성을 위임한다. (서브클래스의 constructor에서 반드시 super를 호출해야 하는 이유)

2. 수퍼클래스의 인스턴스 생성과 this 바인딩

   인스턴스는 `new.target`이 가리키는 서브클래스가 생성한 것으로 처리

   생성된 인스턴스의 프로토타입은 수퍼클래스의 `prototype` 프로퍼티가 가리키는 객체가 아니라 `new.target`, 즉 서브클래스의 `prototype` 프로퍼티가 가리키는 객체다.

3. 수퍼클래스의 인스턴스 초기화

   this에 바인딩 되어있는 인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

4. 서브클래스 constructor로의 복귀와 this 바인딩

   super의 호출이 종료되면 **super가 반환한 인스턴스가 this에 바인딩된다.**

   **서브클래스는 별도의 인스턴스를 생성하지 않고 super가 반환한 인스턴스를 this에 바인딩하여 그대로 사용한다.**

   super가 호출되지 않으면 인스턴스가 생성되지 않으며, this 바인딩도 할 수 없다.

   서브클래스의 constructor에서 super를 호출하기 전에는 this를 참조할 수 없는 이유가 이 때문

5. 서브클래스의 인스턴스 초기
6. 인스턴스 반환

**✅ 표준 빌트인 생성자 함수 확장**

Array.prototype 의 메서드 중에서 map, filter와 같이 새로운 배열을 반환하는 메서드가 MyArray 클래스의 인스턴스를 반환한다.

만약 새로운 배열을 반환하는 메서드가 MyArray 클래스의 인스턴스를 반환하지 않고 Array의 인스턴스를 반환하면 MyArray 클래스의 메서드와 메서드 체이닝이 불가능하다.

```jsx
console.log(myArray.filter((v) => v % 2) instanceof MyArray); // true

// 메서드 체이닝
console.log(
  myArray
    .filter((v) => v % 2)
    .uniq()
    .average()
); // 2
```
