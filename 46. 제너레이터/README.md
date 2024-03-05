# 46. 제너레이터

## 46.1 제너레이터란 ?

✅ 제너레이터

ES6에서 도입되었으며 코드 블록의 실행을 일시 중지 했다가 필요한 시점에 재개할 수 있는 특수한 함수

✅ 제너레이터와 일반 함수의 차이

1. 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있다.
   - 일반 함수를 호출하면 제어권이 함수에게 넘어가고 함수 코드를 일괄 실행한다. 즉, 함수 호출자는 함수를 호출한 이후 함수 실행을 제어할 수 없다.
   - 제너레이터 함수는 함수 실행을 함수 호출자가 제어할 수 있다. 즉, 함수의 제어권을 함수가 독점하는 것이 아니라 함수 호출자에게 양도(yield)할 수 있다.
2. 제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있다.
   - 일반 함수는 함수가 실행되고 있는 동안에는 함수 외부에서 함수 내부로 값을 전달하여 함수의 상태를 변경할 수 없다.
   - 제너레이터 함수는 함수 호출자에게 상태를 전달할 수 있고 함수 호출자로부터 상태를 전달받을 수도 있다. (양방향)
3. 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
   - 제너레이터 함수를 호출하면 함수 코드를 실행하는 것이 아니라 이터러블이면서 동시에 이터레이터인 제너레이터 객체를 반환한다.

## 46.2 제너레이터 함수의 정의

✅ `function*` 키워드로 선언하며, 하나 이상의 yield 표현식을 포함한다.

✅ \*의 위치는 function 키워드와 함수 이름 사이라면 어디든지 상관없지만, function 바로 뒤에 붙이는 것을 권장한다.

✅ 화살표 함수로 정의할 수 없다.

✅ new 연산자와 함께 생성자 함수로 호출할 수 없다.

```jsx
// 제너레이터 함수 선언문
function* getDecFunc() {
    yield 1;
}

// 제너레이터 함수 표현식
const getExpFunc = function* (){
    yield 1;
}

// 제너레이터 메서드
const obj = {
    * genObjMethod() {
        yield 1;
    }
};

// 제너레이터 클래스 메서드
const MyClass {
    * genClsMethod() {
        yield 1;
    }
}
```

## 46.3 제너레이터 객체

제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니라 제너레이터 객체를 생성해 반환한다.

제너레이터 함수가 반환한 제너레이터 객체는 이터러블이면서 동시에 이터레이터다.

next 메서드를 갖는 이터레이터이지만 이터레이터에는 없는 return, throw 메서드를 갖는다.

✅ next : 제너레이터 함수의 yield 표현식까지 코드 블록을 실행하고 yield 된 값을 value 프로퍼티 값으로, false를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환

✅ return: 인수로 전달받은 값을 value 프로퍼티 값으로, false를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환

✅ throw: 인수로 전달받은 error를 발생시키고 undefined를 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환

```jsx
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (error) {
    console.log(error);
  }
}

const generator = genFunc();

console.log(generator.next()); // {value: 1, done: false}
console.log(generator.return("End!")); // {value: 'End!', done: true}
console.log(generator.throw("Error!")); // {value: undefined, done: true}
```

## 46.4 제너레이터의 일시 중지와 재개

yield 키워드는 제너레이터 함수의 실행을 일시 중지 시키거나 yield 키워드 뒤에 오는 표현식의 평가 결과를 제너레이터 함수 호출자에게 반환한다.

1. 제너레이터 객체의 next 메서드를 호출하면 yield 표현식까지 실행되고 일시 중지된다. 이때 함수의 제어권이 호출자로 양도(yield)된다.
2. 제너레이터 객체의 next 메서드는 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환한다
3. 제너레이터 함수의 yield 표현식까지 코드 블록을 실행하고 yield 된 값을 value 프로퍼티 값으로, done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 불리언 값이 할당된다.
4. 제너레이터 객체의 next 메서드에 전달한 인수는 제너레이터 함수의 yield 표현식을 할당받는 변수에 할당된다.

```jsx
function* genFunc() {
  // 첫번째 next 호출
  // yield 1값이 value에 할당
  // x는 아직 할당 x, next가 두번째 호출될 때 결정
  const x = yield 1;

  // 두번째 next 호출, 전달한 인수 10은 첫번째 yield의 x에 할당
  // const x = yield 1; 완료
  const y = yield x + 10;

  // 세번째 next 호출, 전달한 인수 20은 두 번째 yield의 y에 할당
  // const y = yield (x + 10); 완료
  // 제너레이터의 반환강ㅂㅅ은 의미가 없으니 return은 종료의 의미로만 사용할 것
  return x + y;
}

const generator = genFunc(0);

let res = generator.next();
console.log(res); // {value: 1, done: false}

res = generator.next(10);
console.log(res); // {value: 20, done: false}

res = generator.next(20);
console.log(res); // {value: 30, done: true}
```

## 46.5 제너레이터의 활용

✅ 이터러블의 구현

```jsx
const infiniteFibonacci = (function* () {
  let [pre, cur] = [0, 1];

  while (true) {
    [pre, cur] = [cur, pre + cur];
    yield cur;
  }
})();

for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num);
}
```

✅ 비동기 처리

## 46.6 async/await

async/await는 프로미스를 기반으로 동작하며, 후속 처리 메서드 없이 마치 동기 처리처럼 프로미스가 처리 결과를 반환하도록 구현할 수 있다.

```jsx
const fetch = require("node-fetch");

async function fetchTodo() {
  const url = "~/todos/1";

  const response = await fetch(url);
  const todo = await response.json();
}

fetchTodo();
```

✅ async 함수

await 키워드는 반드시 async 함수 내부에서 사용해야 한다.

async는 언제나 프로미스를 반환하며, 암묵적으로 반환값을 resolve하는 프로미스를 반환한다.

또한 클래스의 constructor 메서드는 async 메서드가 될 수 없다.

```jsx
async function foo(n) { return n; }
foo(1).then(v => console.log(v)); // 1

const bar = async function foo(n) { return n; }
bar(2).then(v => console.log(v)); // 2

const baz = async n => n;
baz(3).then(v => console.log(v))); // 3

const obj = {
    async foo(n) { return n; }
};
obj.foo(4).then(v => console.log(v)); // 4

class MyClass {
    async bar(n) { return n; }
}

const myClass = new MyClass();
myClass.bar(5).then(v => console.log(v)); // 5
```

✅ await 키워드

프로미스가 settled 상태가 될 때까지 기다렸다가 settled 상태가 되면 프로미스가 resolve한 처리 결과를 반환

await 키워드는 다음 실행을 일시 중지시켰다가 프로미스가 settled 상태가 되면 다시 재개한다.

✔️ 처리 순서가 보장되지 않아도 되는 경우

```jsx
async function foo() {
  const res = await Promise.all([
    Promise((resolve) => setTimeout(() => resolve(1), 3000)),
    Promise((resolve) => setTimeout(() => resolve(2), 2000)),
    Promise((resolve) => setTimeout(() => resolve(3), 1000)),
  ]);

  console.log(res);
}

foo(); // 약 3초 처리
```

✔️ 처리 순서가 보장되어야 할 경우

```jsx
async function bar(n) {
  const a = await new Promise((resolve) => setTimeout(() => resolve(n), 3000));
  const b = await new Promise((resolve) =>
    setTimeout(() => resolve(a + 1), 2000)
  );
  const c = await new Promise((resolve) =>
    setTimeout(() => resolve(b + 1), 1000)
  );
  console.log([a, b, c]);
}

bar(1); // 약 6초 처리
```

✅ 에러 처리

에러는 호출자 방향으로 전파된다.

즉, 콜 스택의 아래 방향 (실행 중인 컨텍스트가 푸시되기 직전에 푸시한 실행 컨텍스트 방향)으로 전파

하지만 비동기 함수의 콜백 함수를 호출한 것은 비동기 함수가 아니기 때문에 try ~ catch문을 사용해 에러를 캐치할 수 없다.

async/await에서 에러 처리는 try ~ catch 문을 사용할 수 있다.

catch를 사용하면 모든 에러를 캐치할 수 있다.

```jsx
const fetch = require("node-fetch");

const foo = async () => {
  try {
    const url = "~/todos/1";

    const response = await fetch(url);
    const todo = await response.json();
  } catch (err) {
    console.error(err);
  }
};

foo();
```

async 함수 내에서 catch문을 사용해서 에러처리를 하지 않으면 async 함수는 발생한 에러를 reject하는 프로미스를 반환한다.

```jsx
const fetch = require("node-fetch");

const foo = async () => {
  const url = "~/todos/1";

  const response = await fetch(url);
  const todo = await response.json();
};

foo().then(console.log).catch(console.error);
```
