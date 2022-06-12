# 익명 함수(Anonymous Function)

## 익명 함수란

익명 함수란 이름을 가지지 않은 함수입니다. 따라서 익명 함수는 최초 생성 이후에는 접근할 수 없습니다.

<br>

## 익명 함수의 선언

익명 함수는 아래 코드처럼 `function`과 `()` 사이에 함수 이름을 적지 않고 선언합니다.

```javascript
// 이후 익명 함수를 호출하기 위해서, 익명 함수를 `show` 변수에 담아둡니다.
let show = function () {
    console.log("Anonymous function");
};

show();
```

<br>

## 익명 함수의 사용

### 1. 다른 함수의 인자

```javascript
setTimeout(function () {
    console.log("Execute later after 1 second");
}, 1000);
```

위 코드에서 익명 함수를 `setTimeout()`의 인자로 넘겨주었습니다. `setTimeout()`은 1초 뒤에 익명 함수를 실행합니다.

### 2. 함수 선언 직후에 실행

```javascript
(function () {
    console.log("Immediately invoked function execution");
})();
```

위 코드는 아래 과정으로 동작합니다.

우선, 아래 코드를 통해 함수를 선언합니다.

```javascript
(function () {
    console.log("Immediately invoked function execution");
});
```

이후, `()`를 통해 함수를 호출합니다.

이 경우, 함수에 인자를 전달해줄 수 있습니다.

```javascript
let person = {
    firstName: "John",
    lastName: "Doe",
};

(function () {
    console.log(`${person.firstName} ${person.lastName}`);
})(person);
```

## 화살표 함수

ES6에서는 화살표 함수를 통해 익명 함수 선언을 간단히 할 수 있습니다.

```javascript
// without arrow function
let show = function () {
    console.log("Anonymous function");
};

let add = function (a, b) {
    return a + b;
};

// with arrow function
let show = () => console.log("Anonymous function");

let add = (a, b) => a + b;
```

<br>

## Reference

https://www.javascripttutorial.net/javascript-anonymous-functions/
