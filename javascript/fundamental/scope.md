# 스코프(Scope)

## 스코프란

현재 실행되는 컨텍스트를 말합니다. 여기서 컨텍스트는 값과 표현식이 "표현"되거나 참조될 수 있음을 의미합니다. 만약 변수 또는 다른 표현식이 "해당 스코프" 내에 있지 않다면 사용할 수 없습니다. 스코프는 계층적인 구조를 가지기 때문에 하위 스코프는 상위 스코프에 접근할 수 있지만 반대는 불가능합니다.

### JavaScript 스코프의 특징

대부분의 C-family language는 블록 스코프를 따르지만, JavaScript는 함수 스코프를 따릅니다. 단, ES6에서 도입된 `let`이나 `const` 키워드를 사용하면 블록 스코프를 사용할 수 있습니다.

<br>

## 스코프의 종류

### 1. 전역 스코프(global scope)

전역에 변수를 선언하면 이 변수는 어디서든지 참조할 수 있는 전역 스코프를 갖는 전역 변수가 됩니다.

```javascript
var global = "global";

function foo() {
    var local = "local";
    console.log(global); // global
    console.log(local); // local
}
foo();

console.log(global); // global
console.log(local); // Uncaught ReferenceError: local is not defined
```

### 1-1. 비 블록 스코프(non block scope)

JavaScript에서 `var` 키워드는 블록 스코프에 영향을 받지 않으므로 함수 밖에서 `var`로 선언된 변수는 코드 블록 내에서 선언되었다할지라도 모두 전역 스코프를 갖습니다.

```javascript
if (true) {
    var x = 5; // x는 전역 변수
}
console.log(x); // 5
```

### 2. 지역 스코프(local scope, function scope)

지역이란 함수 몸체 내부를 말합니다. 함수 내에서 선언된 매개변수와 변수는 함수 외부에서는 유효하지 않습니다.

```javascript
var a = 10; // 전역 변수

(function () {
    var b = 20; // 지역 변수
})();

console.log(a); // 10
console.log(b); // Uncaught ReferenceError: b is not defined
```

전역 변수와 지역 변수의 변수명이 중복된 경우, 전역 영역에서는 전역 변수만이 참조 가능하고 함수 내 지역 영역에서는 전역과 지역 모두 참조 가능하나 지역 변수를 우선하여 참조합니다.

```javascript
var x = "global";

function foo() {
    var x = "local";
    console.log(x); // local
}
foo();

console.log(x); // global
```

함수 영역에서 전역 변수를 참조할 수 있으므로 전역 변수의 값을 변경할 수 있습니다.

```javascript
var x = 10;

function foo() {
    x = 100;
    console.log(x); // 100
}
foo();

console.log(x); // 100
```

중첩 스코프는 가장 인접한 지역을 우선하여 참조합니다.

```javascript
var x = 10;

function foo() {
    var x = 100;
    console.log(x); // 100

    function bar() {
        x = 1000;
        console.log(x); // 1000
    }
    bar();

    console.log(x); // 1000
}
foo();

console.log(x); // 10
```

### 3. 블록 스코프(block scope)

ES6에서 `let`과 `const` 키워드와 함께 블록 스코프가 생겨났습니다. 블록 스코프는 블록(`{}`) 내부에서 선언된 변수는 블록 외부에서 접근할 수 없다는 것을 의미합니다.

`var` 키워드로 생성된 변수는 블록 스코프를 가지지 않습니다. 즉, 블록 내부에서 선언된 변수도 블록 외부에서 접근할 수 있습니다.

```javascript
{
    let letVariable = 1;
    var varVariable = 2;
}

console.log(letVariable); // Uncaught ReferenceError: letVariable is not defined
console.log(varVariable); // 2
```

<br>

## 렉시컬 스코프(Lexical Scope)

```javascript
var x = 1;

function foo() {
    var x = 10;
    bar();
}

function bar() {
    console.log(x);
}

foo();
bar();
```

위 예시의 실행 결과는 함수 `bar()`의 상위 스코프가 무엇인지에 따라 결정됩니다. 프로그래밍 언어는 두 가지 방식 중 하나의 방식으로 함수의 상위 스코프를 결정합니다.

1. 동적 스코프(dynamic scope)
    - 함수를 어디서 호출하였는지에 따라 상위 스코프를 결정합니다.
    - 함수 `bar()`의 상위 스코프는 함수 `foo()`와 전역이 됩니다. (10, 1)
2. 렉시컬 스코프(lexical scope) 또는 정적 스코프(static scope)
    - 함수를 어디서 선언하였는지에 따라 상위 스코프를 결정합니다.
    - 함수 `bar()`의 상위 스코프는 전역이 됩니다. (1, 1)

<br>

## 암묵적 전역(Implicit Global)

선언하지 않은 식별자에 값을 할당하면 전역 객체의 프로퍼티가 되어 전역 변수처럼 동작하는 현상을 암묵적 전역이라고 합니다.

```javascript
var x = 10; // 전역 변수

function foo() {
    y = 20; // 선언하지 않은 식별자
    console.log(x + y); // 30
}

foo();
```

위 예시에서 `foo()` 함수가 호출되면 JavaScript 엔진은 변수 y에 값을 할당하기 위해 스코프 체인을 통해 선언된 변수인지 확인합니다. 이때, `foo()` 함수의 스코프와 전역 스코프 어디에서도 변수 y의 선언을 찾을 수 없으므로 참조 에러가 발생해야 하지만 JavaScript 엔진은 `y = 20`을 `window.y = 20`으로 해석하여 프로퍼티를 동적 생성합니다.

y는 변수 선언 없이 단지 전역 객체의 프로퍼티로 추가되었을 뿐입니다. 따라서 y는 변수가 아니고, 변수 호이스팅도 발생하지 않습니다.

```javascript
console.log(x); // undefined. 전역 변수 x는 호이스팅이 발생합니다.
console.log(y); // ReferenceError. 전역 변수가 아니라 단지 전역 프로퍼티인 y는 호이스팅이 발생하지 않습니다.

var x = 10;

function foo() {
    y = 20;
    console.log(x + y); // 30
}

foo();
```

또한 y는 변수가 아니라 프로퍼티이므로 `delete` 연산자로 삭제할 수 있습니다. 전역 변수는 전역 객체의 프로퍼티이지만 `delete` 연산자로 삭제할 수 없습니다.

```javascript
var x = 10; // 전역 변수

function foo() {
    y = 20;
}

foo();

console.log(window.x); // 10
console.log(window.y); // 20

delete x; // 전역 변수는 삭제되지 않습니다.
delete y; // 프로퍼티는 삭제됩니다.

console.log(window.x); // 10
console.log(window.y); // undefined
```

<br>

## 전역 변수로 변수를 선언하면 생기는 문제점

### 1. 암묵적 결합

전역 변수를 선언한 의도는 전역 즉, 코드 어디서든 참조하고 할당할 수 있는 변수를 사용하겠다는 것입니다. 이는 모든 코드가 전역 변수를 참조하고 변경할 수 있는 암묵적 결합을 허용하는 것입니다. 변수의 유효 범위(스코프)가 클수록 코드의 가독성은 나빠지고 의도치 않게 상태가 변경될 수 있는 위험성도 커집니다.

### 2. 변수의 긴 생명 주기

전역 변수는 생명 주기가 깁니다. 따라서 메모리 리소스도 오랜 기간 소비합니다. 또한 전역 변수의 상태를 변경할 수 있는 시간도 길고 기회도 많습니다. 변수 이름이 중복되기라도 한다면 의도치 않은 재할당이 이뤄지기도 합니다.

### 3. 스코프 체인 상에서 종점에 존재

전역 변수는 스코프 체인 상에서 종점에 존재합니다. 이는 변수를 검색할 때 전역 변수가 가장 마지막에 검색된다는 것을 의미합니다. 즉, 전역 변수의 검색 속도가 가장 느립니다. 검색 속도의 차이는 그다지 크지 않지만 속도의 차이는 분명히 존재합니다.

### 4. 네임스페이스 오염

JavaScript의 가장 큰 문제점 중 하나는 파일이 분리되어 있다 해도 하나의 전역 스코프를 공유한다는 것입니다. 따라서 다른 파일 내에서 동일한 이름으로 명명된 전역 변수나 전역 함수가 같은 스코프 내에 존재할 경우 예상치 못한 결과를 가져올 수 있습니다.

### (참고) IIFE를 사용하면 전역 변수 사용을 억제할 수 있습니다.

<br>

## Reference

-   [MDN Web Docs - "스코프"](https://developer.mozilla.org/ko/docs/Glossary/Scope)
-   [PoiemaWeb - "5.15 스코프"](https://poiemaweb.com/js-scope)
-   [junh0328/prepare_frontend_interview - "스코프"](https://github.com/junh0328/prepare_frontend_interview/blob/main/JS.md#%EC%8A%A4%EC%BD%94%ED%94%84)
