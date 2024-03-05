# 48. 모듈

## 48.1 모듈의 일반적 의미

✅ 모듈이란 ?

애플리케이션을 구성하는 개별적 요소로서 재사용 가능한 코드 조각

일반적으로 기능을 기준으로 파일 단위로 분리하며, 모듈이 성립하려면 **자신만의 파일 스코프(모듈 스코프)** 를 가질 수 있어야 한다.

**모듈은 공개가 필요한 자산에 한정하여 명시적으로 선택적 공개가 가능하다. (export)**

**모듈 사용자는 모듈이 공개(export)한 자산 중 일부 또는 전체를 선택해 자신의 스코프 내로 불러들여 재사용할 수 있다. (import)**

## 48.2 자바스크립트와 모듈

자바스크립트는 모듈이 성립하기 위해 필요한 파일 스코프와 import, export를 지원하지 않았다.

모든 자바스크립트 파일은 하나의 전역을 공유하기 때문에 분리된 파일이라 할지라도 전역 변수가 중복되는 등의 문제가 발생할 수 있다.

자바스크립트 런타임 환경인 Node.js가 CommonJS 라이브러리를 채택하고, 모듈 시스템을 지원하게 되면서 Node.js 환경에서는 파일 별로 독립적인 파일 스코프(모듈 스코프)를 갖게 되었다.

## 48.3 ES6 모듈 (ESM)

✅ 사용 방법

script 태그에 `type=”module”` 어트리뷰트를 추가하면 로드된 자바스크립트 파일이 모듈로서 동작

일반적인 자바스크립트 파일이 아닌 ESM을 명확하게 하기 위해 ESM의 파일 확장자는 mjs를 사용할 것을 권장

기본적으로 strict mode가 적용

```jsx
<script type="module" src="app.mjs"></script>
```

✅ 모듈 스코프

모듈 내에서 선언한 식별자는 모듈 스코프가 다르기 때문에 모듈 외부에서 참조할 수 없다.

```html
<!DOCTYPE html>
<html>
  <body>
    <script type="module" src="foo.mjs"></script>
    <script type="module" src="bar.mjs"></script>
  </body>
</html>
```

```jsx
// foo.mjs
const x = "foo";
console.log(x); // foo
```

```jsx
// bar.mjs
console.log(x); // ReferenceError: x is not defined
```

✅ export

모듈 내부에서 선언한 식별자를 외부에 공개하여 다른 모듈들이 재사용할 수 있게 하려면 export 키워드를 사용

export 키워드는 선언문 앞에 사용하며, 변수, 함수, 클래스 등 모든 식별자를 export 할 수 있다.

```jsx
// 변수
export const = pi = Math.PI;

// 함수
export function square(x) {
    return x * x;
}

// 클래스
export class Person {
    constructor(name) {
        this.name = name;
    }
}
```

```jsx
// lib.mjs
const = pi = Math.PI;
function square(x) {
    return x * x;
}
class Person {
    constructor(name) {
        this.name = name;
    }
}

// 하나의 객체로 구성하여 공개
export {pi, square, Person};
```

✅ import

다른 모듈에서 공개한 식별자를 자신의 모듈 스코프 내부로 로드할 때 Import 키워드를 사용한다.

다른 모듈이 export한 식별자 이름으로 import 해야 하며 ESM의 경우 파일 확장자를 생략할 수 없다.

```jsx
// app.mjs
// ESM의 경우 파일 확장자를 생략할 수 없다.
import { pi, square, Person } from "./lib.mjs";
```

```html
<!DOCTYPE html>
<html>
  <body>
    <script type="module" src="app.mjs"></script>
  </body>
</html>
```

app.mjs는 애플리케이션의 진입점이므로 반드시 script 태그로 로드해야 한다.

하지만 lib.mjs는 import 문에 의해 로드되는 의존성이기 때문에, script 태그로 로드하지 않아도 된다.

1. 이름을 지정하지 않고 하나의 이름으로 한 번에 import할 수도 있다.
2. 모듈이 export한 식별자 이름을 변경하여 import할 수도 있다.

```jsx
// 1. 모든 식별자를 Lib 객체의 프로퍼티로 모아 Import
import * as lib from "./lib.mjs";

console.log(lib.pi);

// 2. 모듈이 Export한 식별자 이름 변경
import { pi as PI, square as sq, Person as p } from "./lib.mjs";

console.log(PI);
```

1. 모듈에서 하나의 값만 export 한다면 default 키워드를 사용할 수 있지만, var let const 키워드는 사용할 수 없다.
2. default 키워드와 함께 export 한 모듈은 {} 없이 임의의 이름으로 import 한다.
