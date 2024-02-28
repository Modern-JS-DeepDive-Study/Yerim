## 45.1 비동기 처리를 위한 콜백 패턴의 단점

### **✅ 콜백 헬**

비동기 함수를 호출하면 함수 내부의 비동기로 동작하는 코드가 완료되지 않았다 해도 기다리지 않고 즉시 종료된다. 즉, 비동기 함수 내부의 비동기로 동작하는 코드는 비동기 함수가 종료된 이후에 완료된다.

비동기 함수 내부의 비동기로 동작하는 코드에서 처리 결과를 외부로 반환하거나 상위 스코프의 변수에 할당하면 기대한 대로 동작하지 않는다.

**✅ 외부에 반환했을 때**

```jsx
const get = (url) => {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      return JSON.parse(xhr.response);
    }
    console.error(`${xhr.status} ${xhr.statusText}`);
  };
};

const response = get("~/posts/1");
console.log(response); // undefined
```

- get 함수를 호출하면 GET요청을 전송하고 onload 이벤트 핸들러를 등록한 다음 undefined를 반환하고 즉시 종료된다.
- 비동기 함수인 get 함수 내부의 onload 이벤트 핸들러는 get 함수가 종료된 이후에 실행된다.
- 해당 반환문은 get 함수의 반환문이 아니다.

**✅ 상위 스코프에 할당했을 때**

```jsx
let todos;

const get = (url) => {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      todos = JSON.parse(xhr.response);
    } else {
      console.error(`${xhr.status} ${xhr.statusText}`);
    }
  };
};

const response = get("~/posts/1");
console.log(todos); // ② undefined
```

- 바인딩한 이벤트 핸들러는 언제나 **② console.log가 종료한 이후에 호출**
- ②의 시점에는 todos에 서버의 응답이 할당되기 이전

**✨ xhr.onload 이벤트 핸들러 프로퍼티에 바인딩한 이벤트한 이벤트 핸들러가 즉시 실행되는 것이 아니다.**

**xhr.onload 이벤트 핸들러는 load이벤트가 발생하면 일단 태스크 큐에 저장되어 대기하다가, 콜 스택이 비면 이벤트 루프에 의해 콜 스택으로 푸시되어 실행된다.**

🔎 비동기 함수는 비동기 처리 결과를 외부에 반환할 수 없고, 상위 스코프의 변수에 할당할 수도 없다.

비동기 함수의 처리 결과에 대한 후속 처리는 비동기 함수 내부에서 수행해야 한다. ⇒ 콜백 함수 전달

**✨ 콜백 헬:** 콜백 함수를 통해 비동기 처리 결과에 대한 후속 처리를 수행하는 비동기 함수가 비동기 처리 결과를 가지고 또다시 비동기 함수를 호출해야 한다면 콜백 함수 호출이 중첩되어 복잡도가 높아지는 현상이 발생

### **✅ 에러 처리의 한계**

에러는 호출자 방향으로 전파된다.

하지만 setTimeout 함수의 콜백 함수를 호출한 것은 setTimeout 함수가 아니다.

따라서 setTimeout 함수의 콜백 함수가 발생시킨 에러는 catch 블록에서 캐치되지 않는다.

## 45.2 프로미스의 생성

Promise 생성자 함수를 new 연산자와 함께 호출하면 프로미스를 생성한다.

또한 비동기 처리를 수행할 콜백함수를 인수로 전달받는데 resolve와 reject 함수를 인수로 전달받는다.

```jsx
const promise = new Promise((resolve, reject) => {
	// promise 함수의 콜백 함수 내부에서 비동기 처리를 수행한다.
	if (/* 비동기 처리 성공 */) {
		resolve('result');
	} else { // 비동기 처리 실패
		reject('failure reason');
	}
});
```

| 프로미스의 상태 정보 | 의미                                  | 상태 변경 조건                   |
| -------------------- | ------------------------------------- | -------------------------------- |
| pending              | 비동기 처리가 아직 수행되지 않은 상태 | 프로미스가 생성된 직후 기본 상태 |
| fulfilled            | 비동기 처리가 수행된 상태(성공)       | resolve 함수 호출                |
| rejected             | 비동기 처리가 수행된 상태(실패)       | reject 함수 호출                 |

프로미스는 pending 상태에서 fulfilled 또는 rejected 상태, 즉 settled 상태로 변화할 수 있다. 하지만 settled 상태가 되면 더는 다른 상태로 변화할 수 없다.

**✨ 프로미스: 비동기 처리 상태와 처리 결과를 관리하는 객체**

## 45.3 프로미스의 후속 처리 메서드

프로미스는 비동기 처리 상태가 변화하면 **후속 처리 메서드**에 인수로 전달한 콜백 함수가 선택적으로 호출된다.

fulfilled든 rejected든 반환한 결과 값에 대해 후속 처리를 해야하는데, 그때 then, catch, finally를 제공한다.

### **✅ Promise.prototype.then**

두 개의 콜백 함수를 인수로 전달 받는다.

첫 번째는 비동기 처리가 성공했을 떄 호출되는 성공 처리 콜백 함수이며, 두 번째 콜백 함수는 비동기 처리가 실패했을 때 호출되는 실패 처리 콜백 함수이다.

```jsx
new Promise((resolve) => resolve("fulfilled")).then(
  (v) => console.log(v),
  (e) => console.error(e)
);
```

### **✅ Promise.prototype.catch**

한 개의 콜백 함수만 인수로 전달받으며, 프로미스가 rejected 상태인 경우에만 호출된다.

```jsx
new Promise((_, reject) => reject(new Error("rejected"))).catch((e) =>
  console.error(e)
);
// 위와 동일
new Promise((_, reject) => reject(new Error("rejected"))).then(undefined, (e) =>
  console.error(e)
);
```

### **✅ Promise.prototype.finally**

한 개의 콜백 함수를 인수로 전달받으며, 성공 또는 실패와 상관없이 무조건 한 번 호출된다.

```jsx
new Promise(() => {}).finally(() => console.log("finally"));
```

## 45.4 프로미스의 에러 처리

then 메서드의 두 번째 콜백 함수는 첫 번째 콜백 함수에서 발생한 에러를 캐치하지 못한다.

catch 메서드를 모든 then 메서드를 호출한 이후에 호출하면 비동기 처리에서 발생한 에러 뿐만 아니라 then 메서드 내부에서 발생한 에러까지 모두 캐치할 수 있다.

```jsx
promiseGet('~/todos/1')
	.then(res => console.log(res))
	.catch(err => console.error(err)))
```

## 45.5 프로미스 체이닝

**✅ 프로미스 체이닝**

then, catch, finally 후속 처리 메서드는 언제나 프로미스를 반환하므로 연속적으로 호출할 수 있다.

## 45.6 프로미스의 정적 메서드

### **✅ Promise.resolve / Promise.reject**

이미 존재하는 값을 래핑하여 프로미스를 생성하기 위해 사용

resolve와 reject 모두 인수로 전달받은 값을 resolve, reject 하는 프로미스를 생성

```jsx
const resolvedPromise = new Promise((resolve) => resolve([1, 2, 3]));
resolvedPromise.then(console.log); // [1, 2, 3]
```

### **✅ Promise.all**

여러 개의 비동기 처리를 모두 병렬 처리할 때 사용

```jsx
const requestData1 = () =>
  new Promise((resolve) => setTimeout(() => resolve(1), 3000));
const requestData2 = () =>
  new Promise((resolve) => setTimeout(() => resolve(2), 2000));
const requestData3 = () =>
  new Promise((resolve) => setTimeout(() => resolve(3), 1000));

// 3개의 비동기 처리를 병렬로 처리
Promise.all([requestData1(), requestData2(), requestData3()])
  .then(console.log) // [1, 2, 3] => 약 3초 소요
  .catch(console.error);
```

- Promise.all 메서드가 종료하는데 걸리는 시간은 가장 늦게 fulfilled 상태가 되는 프로미스의 처리 시간보다 조금 더 길다.
- 처리 결과를 배열로 저장하며, 처리 순서는 보장된다.
- 하나라도 rejected 상태가 되면 나머지 프로미스가 fulfilled 상태가 되는 것을 기다리지 않고 즉시 종료한다.

### **✅ Promise.race**

```jsx
Promise.race([
  new Promise((resolve) => setTimeout(() => resolve(1), 3000)),
  new Promise((resolve) => setTimeout(() => resolve(2), 2000)),
  new Promise((resolve) => setTimeout(() => resolve(3), 1000)),
])
  .then(console.log) // 3
  .catch(console.log);
```

- Promise.all 메서드처럼 모든 프로미스가 fulfilled 상태가 되는 것을 기다리는 것이 아니라 가장 먼저 fulfilled 상태가 된 프로미스의 처리 결과를 resolve하는 새로운 프로미스를 반환한다.
- 하나라도 rejected 상태가 되면 나머지 프로미스가 fulfilled 상태가 되는 것을 기다리지 않고 즉시 종료한다.

### **✅ Promise.allSettled**

전달받은 프로미스가 모두 settled 처리가 되면 처리 결과를 배열로 반환

## 45.7 마이크로태스크 큐

✅ 프로미스의 후속 처리 메서드의 콜백 함수는 태스크 큐가 아니라 마이크로태스크 큐에 저장된다.

✅ 프로미스의 후속 처리 메서드의 콜백 함수가 일시 저장

✅ 마이크로태스크 큐는 태스크 큐보다 우선순위가 높음

✅ 이벤트 루프는 콜 스택이 비면 먼저 마이크로태스크 큐에서 대기하고 있는 함수를 가져와 실행

```jsx
setTimeout(() => console.log(1), 0);

Promise.resolve()
  .then(() => console.log(2))
  .then(() => console.log(3));

// 2 -> 3 -> 1
```

## 45.8 fetch

✅ HTTP 응답을 나타내는 Response 객체를 래핑한 Promise 객체를 반환한다.

✨ fetch와 axios의 차이

- fetch

  - 기본적으로 404 Not Found나 500 Internal Server Error 와 같은 HTTP 에러가 발생해도 에러를 reject 하지 않고 불리언 타입의 ok 상태를 false로 설정한 Response 객체를 resolve 한다.
  - 네트워크 장애나 CORS 에러에 의해 요청이 완료되지 못한 경우에만 프로미스를 reject한다.

  ```jsx
  const wrongUrl = "~/type/1";

  fetch(wrongUrl).then((response) => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  });
  ```

- axios
  - 모든 HTTP 에러를 reject 하는 프로미스를 반환
  - 인터셉터, 요청 설정 등 fetch 보다 다양한 기능을 지원
