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

<br>

## Reference

https://h5bp.org/Front-end-Developer-Interview-Questions/translations/korean/#JS-%EA%B4%80%EB%A0%A8-%EC%A7%88%EB%AC%B8
