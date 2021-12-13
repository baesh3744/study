# 타입 변환

## 명시적 타입 변환

개발자가 의도적으로 값의 타입을 변환하는 것을 **명시적 타입 변환** 또는 **타입 캐스팅**이라고 합니다.

```javascript
var x = 10;

// 숫자를 문자열로 타입 캐스팅합니다.
var str = x.toString();
console.log(typeof str, str); // string "10"

// x 변수의 값이 변경된 것은 아닙니다.
console.log(typeof x, x); // number 10
```

명시적 타입 변환 함수에는 `String()`, `.toString()`, `Number()`, `parseInt()`, `Boolean()` 등이 있습니다.

<br>

## 암묵적 타입 변환

개발자의 의도와는 상관없이 표현식을 평가하는 도중에 JavaScript 엔진에 의해 암묵적으로 타입이 자동 변환되는 것을 **암묵적 타입 변환** 또는 **강제 타입 변환**이라고 합니다.

```javascript
var x = 10;

// 문자열 연결 연산자(+)는 숫자 타입 x의 값을 바탕으로 새로운 문자열을 생성합니다.
var str = x + "";
console.log(typeof str, str); // string "10"

// x 변수의 값이 변경된 것은 아닙니다.
console.log(typeof x, x); // number 10
```

<br>

## Reference

-   [junh0328/prepare_frontend_interview - 타입변환과 단축 평가](https://github.com/junh0328/prepare_frontend_interview/blob/main/JS.md#%ED%83%80%EC%9E%85%EB%B3%80%ED%99%98%EA%B3%BC-%EB%8B%A8%EC%B6%95-%ED%8F%89%EA%B0%80)
