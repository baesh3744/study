# 인터뷰 문제들(Interview Questions)

### 다음 코드의 차이점은 무엇인가요?

```javascript
function Person() {}
var person = Person();
var person = new Person();
```

<details>
    <summary>Click!</summary>

<br>

-   `function Person() {}`은 함수 선언문입니다. 따라서 런타임 이전에 선언되며, 호이스팅이 됩니다. 이때, 호출이 되지 않는다면 실행이 되지 않습니다.
-   `var person = Person();`은 함수를 호출하고, 반환값을 변수에 저장하는 명령문입니다.
-   `var person = new Person();`에서 `Person()` 함수는 생성자 함수로써 동작합니다. 따라서 `Person()` 함수가 반환하는 값에 따라 변수 `person`에 저장되는 값이 달라지는데, 반환하는 값이 객체라면 반환한 객체가, 객체 이외의 값이라면 `Person()` 함수의 `this`가 저장됩니다.

</details>

### `Function.prototype.call()`과 `Function.prototype.apply()`의 차이점은 무엇인가요?

<details>
    <summary>Click!</summary>

<br>

두 메소드 모두 함수 내 `this`를 명시적으로 고정해주기 위해 사용하지만, 복수 인자를 받을 때 `call()` 메소드는 인자를 따로따로 받고 `apply()` 메소드는 유사 배열 객체로 받습니다.

</details>

### `Function.prototype.bind()`을 설명하세요.

<details>
    <summary>Click!</summary>

<br>

```javascript
const user = {
    firstName: "Foo",
    sayHello() {
        console.log(`Hello, ${this.firstName}!`);
    },
};

const hello = user.sayHello;
hello(); // Hello, undefined!
```

위 코드와 같이 객체 메소드는 개체 내부가 아닌 다른 곳에 전달되어 호출되면 `this`가 사라집니다.

`hello`에 `user.sayHello`를 전달할 때, `bind()`를 사용하면 `this`를 `user`로 고정할 수 있습니다.

```javascript
const hello = user.sayHello.bind(user);
hello(); // Hello, Foo!
```

이처럼 `bind()`는 함수에 전달되는 `this`를 고정시켜줍니다. 또한 `bind()`의 두 번쨰 인자부터는 원본 함수의 인자로 전달되어 고정시켜줍니다.

</details>

### "호이스팅(Hoisting)"에 대해서 설명하세요.

<details>
    <summary>Click!</summary>

<br>

호이스팅이란 JavaScript 인터프리터가 변수와 함수의 메모리 공간을 선언 전에 미리 할당하는 것을 말합니다. 따라서 변수를 선언하는 코드보다 변수를 사용하는 코드가 먼저 등장할 수 있습니다. 호이스팅 시 `var`로 선언한 변수는 `undefined`로 초기화되지만, `let`이나 `const`로 선언한 변수는 초기화되지 않습니다.

```javascript
console.log(varNum); // undefined
var varNum;

console.log(constNum); // 2
const constNum = 2;
```

아래와 같은 경우에는 `ReferenceError`가 발생하므로 주의해야 합니다.

-   선언 없이 초기화하는 코드만 존재하는 경우

```javascript
console.log(num); // ReferenceError. 선언하는 코드가 존재하지 않으므로 호이스팅이 발생하지 않습니다.
num = 2;
```

-   `let`이나 `const`로 선언한 변수를 초기화 전에 사용하는 경우

```javascript
console.log(num); // ReferenceError. const로 선언한 변수이므로 초기화된 값이 존재하지 않습니다.
const num;
```

</details>

### `let`, `var`, `const`의 차이점에 관해서 설명해주세요.

<details>
    <summary>Click!</summary>

<br>

const는 변하지 않는 변수 즉, 상수를 선언할 때 사용합니다. 상수는 재할당할 수 없으므로 상수를 변경하려고 하면 에러가 발생합니다.

let은 변수를 선언할 때 사용합니다.

var도 let과 마찬가지로 변수를 선언할 때 사용합니다. 일반적으로 var과 let은 바꿔 사용해도 큰 문제가 없지만, var은 let과 다르게 아래와 같은 특징을 가집니다.

1. var은 블록 스코프가 없습니다. var로 선언한 변수의 스코프는 함수 스코프이거나 전역 스코프입니다. 블록 기준으로 스코프가 생성되지 않기 때문에 블록 밖 (if문, for문 등) 에서 변수에 접근이 가능합니다.
2. var은 재선언이 가능합니다.
3. var은 할당 전에 사용이 가능합니다. let으로 선언한 변수는 호이스팅 될 때 초기값이 할당되지 않아 값을 할당한 후 사용할 수 있지만, var로 선언한 변수는 호이스팅 될 때 자동으로 undefined로 값이 할당되어 할당하지 않아도 사용이 가능합니다.

</details>

### "속성(attribute)"와 "요소(property)"의 차이는 무엇인가요?

<details>
    <summary>Click!</summary>

<br>

| Attribute                 | Property              |
| ------------------------- | --------------------- |
| HTML 태그 안에서 사용     | DOM 객체 안에서 사용  |
| 대·소문자를 구분하지 않음 | 대·소문자를 구분함    |
| 문자열 값만 가능          | 모든 타입의 값이 가능 |

(자세히) [속성(Attribute)와 프로퍼티(Property)](JavaScript/attribute-property.md)

</details>

<br>

## Reference

https://h5bp.org/Front-end-Developer-Interview-Questions/translations/korean/#JS-%EA%B4%80%EB%A0%A8-%EC%A7%88%EB%AC%B8
