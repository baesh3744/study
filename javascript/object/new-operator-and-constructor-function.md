# new 연산자와 생성자 함수

객체 리터럴 `{ ... }`을 사용하면 객체를 쉽게 만들 수 있습니다. 그런데 개발을 하다 보면 유사한 객체를 여러 개 만들어야 할 때가 생기곤 합니다. 복수의 사용자, 메뉴 내 다양한 아이템을 객체로 표현하려고 하는 경우가 그렇습니다.

`new` 연산자와 생성자 함수를 사용하면 유사한 객체 여러 개를 쉽게 만들 수 있습니다.

<br>

## 생성자 함수(Constructor Function)

생성자 함수와 일반 함수에 기술적인 차이는 없습니다. 다만 생성자 함수는 아래 두 관례를 따릅니다.

1. 함수 이름의 첫 글자는 대문자로 시작합니다.
2. 반드시 `new` 연산자를 붙여 실행합니다.

```javascript
function User(name) {
    this.name = name;
    this.isAdmin = false;
}

let user = new User("보라");

console.log(user.name); // 보라
console.log(user.isAdmin); // false
```

`new User(...)`를 써서 함수를 실행하면 아래와 같은 알고리즘이 동작합니다.

1. 빈 객체를 만들어 `this`에 할당합니다.
2. 함수 본문을 실행합니다. `this`에 새로운 property를 추가해 `this`를 수정합니다.
3. `this`를 반환합니다.

```javascript
function User(name) {
    // this = {};  (빈 객체가 암시적으로 만들어집니다.)

    this.name = name;
    this.isAdmin = false;

    // return this;  (this가 암시적으로 반환됩니다.)
}
```

이제 `let user = new User("보라")`는 아래 코드를 입력한 것과 동일하게 동작합니다.

```javascript
let user = {
    name: "보라",
    isAdmin: false,
};
```

`new User("보라")` 이외에도 `new User("호진")`, `new User("지민")` 등을 이용하면 손쉽게 사용자 객체를 만들 수 있습니다. 객체 리터럴 문법으로 일일이 객체를 만드는 방법보다 훨씬 간단하고 읽기 쉽게 객체를 만들 수 있게 됩니다.

생성자의 의의는 재사용할 수 있는 객체 생성 코드를 구현하는 것입니다.

⚠️ 모든 함수는 생성자 함수가 될 수 있습니다. `new`를 붙여 실행한다면 어떤 함수라도 위에서 언급한 알고리즘이 실행됩니다.

### `new function () { ... }`

재사용할 필요가 없는 복잡한 객체를 만들어야 할 때는 아래와 같이 코드를 익명 생성자 함수로 감싸주는 방식을 사용할 수 있습니다.

```javascript
let user = new (function () {
    this.name = "John";
    this.isAdmin = false;

    // 사용자 객체를 만들기 위한 코드
    // 지역 변수, 복잡한 로직, 구문 등이 여기에 들어갑니다.
})();
```

위 생성자 함수는 익명 함수이기 때문에 어디에도 저장되지 않습니다. 처음 만들 때부터 단 한 번만 호출할 목적으로 만들었기 때문에 재사용이 불가능합니다. 이렇게 익명 생성자 함수를 이용하면 재사용은 막으면서 코드를 캡슐화 할 수 있습니다.

<br>

## `new.target`과 생성자 함수

`new.target` property를 사용하면 함수가 `new`와 함께 호출되었는지 아닌지를 알 수 있습니다.

일반적인 방법으로 함수를 호출했다면 `new.target`은 `undefined`를 반환합니다. 반면 `new`와 함께 호출한 경우에는 함수 자체를 반환합니다.

```javascript
function User() {
    console.log(new.target);
}

User(); // undefined
new User(); // function User { ... }
```

함수 본문에서 `new.target`을 사용하면 해당 함수가 `new`와 함께 호출되었는지 (in constructor mode) 아닌지 (in regular mode) 를 확인할 수 있습니다.

이를 활용해 일반적인 방법으로 함수를 호출해도 `new`를 붙여 호출한 것처럼 동작하도록 만들 수 있습니다.

```javascript
function User(name) {
    // `new` 없이 호출하면, `new`를 붙여줍니다.
    if (!new.target) {
        return new User(name);
    }

    this.name = name;
}

let bora = User("보라"); // `new User()`를 쓴 것처럼 바꿔줍니다.
console.log(bora.name); // 보라
```

이런 방식을 사용하면 `new`를 붙여 함수를 호출하든 아니든 코드가 동일하게 동작하기 때문에, 좀 더 유연하게 코드를 작성할 수 있습니다.

그런데 이 방법을 믿고 객체를 생성하는 경우에도 `new`를 생략하면 코드가 정확히 무슨 일을 하는지 알기 어렵습니다. `new`가 붙어있으면 새로운 객체를 만든다는 걸 누구나 알 수 있기 때문에 `new`를 생략해서 객체를 만드는 것은 정말 필요한 경우에만 사용하고 남발하면 안됩니다.

<br>

## 생성자 함수와 `return`문

생성자 함수엔 보통 `return`문이 없습니다. 반환해야 할 것들은 모두 `this`에 저장되고, `this`는 자동으로 반환되기 때문에 반환문을 명시적으로 써줄 필요가 없습니다.

그런데 만약 `return`문이 있다면 어떤 일이 벌어질까요? 아래와 같은 간단한 규칙이 적용됩니다.

-   객체를 `return`한다면 `this` 대신 객체가 반환됩니다.
-   원시형을 `return`한다면 `return`문이 무시됩니다.

`return` 뒤에 객체가 오면 생성자 함수는 해당 객체를 반환해주고, 이 외의 경우는 `this`가 반환됩니다.

아래 예시에서는 첫 번째 규칙이 적용돼 `return`은 `this`를 무시하고 객체를 반환합니다.

```javascript
function BigUser() {
    this.name = "원숭이";

    return { name: "고릴라" };
}

console.log(new BigUser().name); // 고릴라
```

아래 예시에서는 두 번째 규칙이 적용돼 `this`를 반환합니다.

```javascript
function SmallUser() {
    this.name = "원숭이";

    return;
}

console.log(new SmallUser().name); // 원숭이
```

⚠️ `return`문이 있는 생성자 함수는 거의 없습니다.

<br>

## 생성자 함수 내 메소드

생성자 함수 내에서 `this`에 property뿐 아니라, 메소드를 더해주는 것도 가능합니다.

```javascript
function User(name) {
    this.name = name;

    this.sayHi = function () {
        console.log("제 이름은 " + this.name + "입니다.");
    };
}

let bora = new User("보라");
bora.sayHi(); // 제 이름은 보라입니다.
```

<br>

## Reference

https://ko.javascript.info/constructor-new
