# null / undefined / undeclared

## null

`null`은 JavaScript의 원시 값 중 하나로, 어떤 값이 의도적으로 비어있음을 표현하며 boolean 연산에서는 거짓으로 취급합니다.

```javascript
// 정의되지 않고 초기화된 적도 없는 foo
console.log(foo); // ReferenceError: foo is not defined

// 존재하지만 값이나 자료형이 존재하지 않는 foo
var foo = null;
console.log(foo); // null
console.log(typeof foo); // object
```

<br>

## undefined

`undefined`는 원시값으로, 선언한 후 값을 할당하지 않은 변수 혹은 값이 주어지지 않은 인수에 자동으로 할당됩니다.

```javascript
var foo; // 값을 할당하지 않고 변수 선언

console.log("foo's value is", foo); // "foo's value is undefined"
console.log(typeof foo); // undefined
```

<br>

## undeclared

접근 가능한 스코프에 변수 선언조차 되어있지 않은 상태를 말합니다. typeof는 undeclared인 경우에 undefined를 반환하여 브라우저가 오류 처리를 하지 않도록 합니다.

```javascript
console.log(foo); // ReferenceError: foo is not defined
console.log(typeof foo); // undefined
```

<br>

## Reference

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/null

https://developer.mozilla.org/ko/docs/Glossary/undefined

https://sudo-heedongdev.tistory.com/6
