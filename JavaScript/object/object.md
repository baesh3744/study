# 객체(Object)

## 객체란

JavaScript에는 8가지 자료형이 있습니다. 이 중 7개는 오직 하나의 데이터 (문자열, 숫자 등) 만 담을 수 있어 **원시형(primitive type)**이라고 부릅니다.

JavaScript에서 원시형을 제외한 모든 것 (함수, 배열 등) 은 객체입니다. 객체는 "키(key): 값(value)" 쌍의 프로퍼티(property)로 구성됩니다. 키는 문자열만 가능하지만, 값은 데이터 집합이나 복잡한 개체(entity) 등 원시형과 달리 다양한 데이터와 모든 자료형이 가능합니다.

<br>

## 함수 vs. 메소드

JavaScript에서 사용할 수 있는 모든 값은 프로퍼티 값이 될 수 있습니다. 프로퍼티 값이 함수일 경우, 일반 함수와 구분하기 위해 메소드(method)라고 부릅니다. 즉, 객체 내부에서 객체의 상태를 참조하고 조작할 수 있는 함수, 객체에 묶여 있는 함수를 의미합니다.

```javascript
const person = {
    name: "Lee",
    hello: function () {
        console.log(`hello: ${this.name}`);
    },
};
```

<br>

## 객체 생성 방법

1. 객체 리터럴 `{...}`

```javascript
const person = { name: "Lee" };
```

2. Object 생성자 함수

```javascript
const person = new Object({ name: "Lee" });
```

3. 생성자 함수

```javascript
function Person(name) {
    this.name = name;
}

const person = new Person("Lee");
```

4. `Object.create()`

```javascript
const person = Object.create({}, { name: { value: "Lee" } });
```

5. 클래스(ES6)

```javascript
class Person {
    constructor(name) {
        this.name = name;
    }
}

const person = new Person("Lee");
```

<br>

## 전역 객체란

-   전역 객체는 코드가 실행되기 이전 단계에 JavaScript 엔진에 의해 생성되는 특수한 객체입니다.
-   브라우저 환경 (클라이언트) 에서는 `window`, Node.js 환경 (서버) 에서는 `global` 이라고 부릅니다.
-   전역 객체는 표준 빌트인 객체 (Object, String, Number, ...) 와 환경에 따른 호스트 객체, `var` 키워드로 선언한 전역 변수와 전역 함수를 프로퍼티로 갖습니다.

<br>

## 원시형 vs. 객체

| 원시형                                                                                      | 객체                                                                                                  |
| ------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| 변경 불가능한 값(immutable value)                                                           | 변경 가능한 값(mutable value)                                                                         |
| 원시 값을 변수에 할당하면 변수에는 실제 값이 저장됩니다.                                    | 객체를 변수에 할당하면 변수에는 참조 값 (메모리 주소) 가 저장됩니다.                                  |
| 원시 값을 갖는 변수를 다른 변수에 할당하면 원본의 값이 복사되어 전달됩니다. (call by value) | 객체를 가리키는 변수를 다른 변수에 할당하면 객체의 참조 값이 복사되어 전달됩니다. (call by reference) |

<br>

## Reference

-   [junh0328/prepare_frontend_interview - "객체 리터럴"](https://github.com/junh0328/prepare_frontend_interview/blob/main/JS.md#%EA%B0%9D%EC%B2%B4-%EB%A6%AC%ED%84%B0%EB%9F%B4)
