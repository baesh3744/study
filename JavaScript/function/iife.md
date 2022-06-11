# IIFE(Immediately Invoked Function Expression)

즉시 호출 함수 표현식(IIFE)은 정의되자마자 즉시 실행되는 JavaScript function을 말합니다.

```javascript
(function () {
    // statements
})();
```

이는 Self-Executing Anonymous Function으로 알려진 디자인 패턴이고 크게 두 부분으로 구성됩니다.

첫 번째는 괄호로 둘러싸인 익명함수(anonymous function)입니다. 이는 전역 스코프에 불필요한 변수를 추가해서 오염시키는 것을 방지할 수 있을 뿐 아니라 IIFE 내부안으로 다른 변수들이 접근하는 것을 막을 수 있는 방법입니다.

두 번째 부분은 즉시 호출 함수를 생성하는 괄호입니다. 이를 통해 JavaScript 엔진은 함수를 즉시 해석해서 실행합니다.

<br>

## 예제

```javascript
(function () {
    var aName = "Barry";
})();

// IIFE 내부에서 정의된 변수는 외부로부터의 접근이 불가능합니다.
aName; // throws "Uncaught ReferenceError: aName is not defined"
```

```javascript
var result = (function () {
    var name = "Barry";
    return name;
})();

// IIFE를 변수에 할당하면 IIFE 자체는 저장되지 않고, 함수가 실행된 결과만 저장됩니다.
results; // "Barry"
```

<br>

## 문제

다음 코드가 즉시 호출 함수 표현식(IIFE)로 동작하지 않는 이유에 관해서 설명하고 IIFE로 만들기 위해서는 어떻게 해야 하는지 설명하시오.

```javascript
function foo(){ }();
```

### 풀이

`function foo(){ }` 부분을 `()`로 감싸지 않았기 때문에, JavaScript에서는 이를 '함수 선언문'으로 인지합니다. 따라서, `()`로 감싸주어 '함수 표현식'이라는 것을 명시적으로 나타내야 합니다.

```javascript
function foo() {
    console.log("Hello World");
}(); // Uncaught SyntaxError: Unexpected token ')'

function () {
    console.log("Hello World");
}(); // Uncaught SyntaxError: Function statements require a function name

(function foo() {
    console.log("Hello World");
})(); // "Hello World"

(function () {
    console.log("Hello World");
})(); // "Hello World"
```

<br>

## Reference

https://developer.mozilla.org/ko/docs/Glossary/IIFE

https://velog.io/@doondoony/javascript-iife
