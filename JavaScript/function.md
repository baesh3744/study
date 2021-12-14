# 함수(Function)

## 함수 생성 방법

1. 함수 선언문

```javascript
function add(x, y) {
    return x + y;
}
```

2. 함수 표현식

```javascript
var add = function (x, y) {
    return x + y;
};
```

3. Function 생성자 함수

```javascript
var add = new Function("x", "y", "return x + y");
```

4. 화살표 함수 (ES6)

```javascript
var add = (x, y) => x + y;
```

<br>

## 함수 표현식 vs. 함수 선언문

### 1. 문법

함수 선언문은 주요 코드 흐름 중간에 독자적인 구문 형태로 존재합니다.

함수 표현식은 함수가 표현식이나 구문 구성(syntax construct) 내부에 생성됩니다. 아래 예시에허는 함수가 할당 연산잔 `=`를 이용해 만든 "할당 표현식" 우측에 생성되었습니다.

```javascript
const sum = function (a, b) {
    return a + b;
};
```

### 2. JavaScript 엔진이 언제 함수를 생성하는가

함수 표현식은 실제 실행 흐름이 해당 함수에 도달했을 때 함수를 생성합니다. 따라서 실행 흐름이 함수에 도달했을 때부터 해당 함수를 사용할 수 있습니다.

JavaScript는 스크립트를 실행하기 전, 준비단계에서 전역에 선언된 함수 선언문을 찾고, 해당 함수를 생성합니다 (함수 호이스팅). 따라서 함수 선언문은 함수 선언문이 정의되기 전에도 호출할 수 있습니다.

### 3. 스코프

Strict mode에서 함수 선언문이 코드 블록 내에 위치하면 해당 함수는 블록 내 어디서든 접근할 수 있습니다. 하지만 블록 밖에서는 함수에 접근하지 못합니다.

```javascript
let age = prompt("나이를 알려주세요.", 18);

// 조건에 따라 함수를 선언합니다.
if (age < 18) {
    function welcome() {
        console.log("안녕!");
    }
} else {
    function welcome() {
        console.log("안녕하세요!");
    }
}

welcome(); // Error: welcome is not defined
```

함수 표현식을 사용하면 if문 밖에서 `welcome()` 함수를 호출할 수 있습니다.

```javascript
let age = prompt("나이를 알려주세요.", 18);

let welcome;

if (age < 18) {
    welcome = function () {
        console.log("안녕!");
    };
} else {
    welcome = function () {
        console.log("안녕하세요!");
    };
}

welcome();
```

물음표 연산자 `?`를 사용하면 위 코드를 단순화할 수 있습니다.

```javascript
let age = prompt("나이를 알려주세요.", 18);

let welcome =
    age < 18
        ? function () {
              console.log("안녕!");
          }
        : function () {
              console.log("안녕하세요!");
          };

welcome();
```

<br>

## Reference

-   [모던 JavaScript 튜토리얼 - "함수 표현식 vs 함수 선언문"](https://ko.javascript.info/function-expressions#ref-584)
