# `const` / `let` / `var`

## `const`

변하지 않는 변수 즉, 상수를 선언할 때, `const`를 사용합니다. 상수는 재할당할 수 없으므로 상수를 변경하려고 하면 에러가 발생합니다.

```javascript
const myBirthday = "18.04.1982";

myBirthday = "18.04.1983"; // Uncaught TypeError: Assignment to constant variable.
```

<br>

## `let`

변수를 선언할 때, `let`을 사용합니다.

```javascript
let message = "This";
```

<br>

## `var`

⚠️ 근래엔 `var`을 사용하지 않습니다.

`var`은 `let`과 마찬가지로 변수를 선언할 때 사용합니다. 대부분의 경우에는 `let`과 `var`을 바꿔 사용해도 큰 문제가 없습니다. 그러나 `var`과 `let`에는 3가지 차이가 존재합니다.

### 1. `var`은 블록 스코프가 없습니다.

`var`로 선언한 변수의 스코프는 함수 스코프이거나 전역 스코프입니다. 블록 기준으로 스코프가 생기지 않기 때문에 블록 밖에서 접근 가능합니다.

```javascript
if (true) {
    var test = "message";
}

console.log(test); // "message". if문이 끝났어도 변수에 접근할 수 있음
```

`var`은 코드 블록을 무시하기 때문에 `test`는 전역 변수가 되어 전역 스코프에서 이 변수에 접근할 수 있습니다.

두 번째 행에서 `var test`가 아닌 `let test`를 사용했더라면, 변수 `test`는 `if`문 안에서만 접근할 수 있습니다.

```javascript
if (true) {
    let test = "message";
}

console.log(test); // Uncaught ReferenceError: test is not defined
```

코드 블록이 함수 안에 있다면, `var`는 함수 레벨 변수가 됩니다.

```javascript
function sayHi() {
    if (true) {
        var phrase = "Hello";
    }

    console.log(phrase); // Hello
}

sayHi();
console.log(phrase); // Uncaught ReferenceError: phrase is not defined
```

### 2. `var`은 재선언을 허용합니다.

스코프 내에서 같은 변수를 `let`으로 두 번 선언하면 에러가 발생하지만, `var`로 선언하면 몇 번이든지 재선언할 수 있습니다.

```javascript
let user;
let user; // Uncaught SyntaxError: Identifier 'user' has already been declared

var user = "Pete";
var user = "John";
console.log(user); // John
```

### 3. `var`은 할당 전에 사용할 수 있습니다.

JavaScript에서 선언은 호이스팅 되지만 할당은 호이스팅 되지 않습니다. `let`이나 `const`로 선언한 변수는 호이스팅 될 때 초기값을 할당하지 않아 값을 할당한 후 사용할 수 있지만, `var`로 선언한 변수는 호이스팅이 될 때 자동으로 `undefined` 값을 할당합니다. 따라서 `var`로 선언한 변수는 할당을 하지 않더라도 사용할 수 있습니다.

```javascript
function sayHi() {
    console.log(phrase); // undefined

    var phrase = "Hello";
}

function sayHi() {
    console.log(phrase); // Uncaught ReferenceError: Cannot access 'phrase' before initialization

    let phrase = "Hello";
}
```

<br>

## Reference

https://ko.javascript.info/variables

https://ko.javascript.info/var
