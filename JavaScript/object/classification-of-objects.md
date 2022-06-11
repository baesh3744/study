# 객체의 분류

## 1. 네이티브 객체(Native Objects, Built-in Objects, Global Objects)

네이티브 객체는 ECMAScript 명세에 정의된 객체를 말하며 애플리케이션 전역에 공통 기능을 제공합니다. 네이티브 객체는 애플리케이션 환경과 관계없이 언제나 사용할 수 있습니다.

Object, String, Number 등 객체 생성에 관계가 있는 함수 객체와 메소드로 구성됩니다.

네이티브 객체를 global objects라고 부르기도 하는데, 이는 전역 객체(global object)와 다른 의미로 사용됩니다.

### 카테고리별 종류

| 카테고리      | 종류                                                                                                          | 설명                                                                                                               |
| ------------- | ------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| 값 속성       | `Infinity`, `NaN`, `undefined`, `null`, `globalThis`                                                          | 간단한 값을 반환하며 속성이나 메소드를 가지고 있지 않습니다.                                                       |
| 기초 객체     | `Object`, `Function`, `Boolean`, `Symbol`                                                                     | 다른 모든 객체의 기반이 되는 기초이자 기본 객체입니다. 일반 객체, 함수, 오류를 나타내는 객체를 포함합니다.         |
| 에러 객체     | `Error`, `EvalError`, `InternalError`, `RangeError`, `ReferenceError`, `SyntaxError`, `TypeError`, `URIError` | 에러 객체는 기초 객체의 특별한 유형으로, 기본적인 `Error` 객체와 함께 특정 용도에 최적화된 에러 형태도 포함합니다. |
| 숫자 및 날짜  | `Number`, `BigInt`, `Math`, `Date`                                                                            | 숫자, 날짜, 수학 계산을 나타내는 기본 객체입니다.                                                                  |
| 텍스트 처리   | `String`, `RegExp`                                                                                            | 문자열을 나타내는 객체로 문자열을 조작할 방법도 제공합니다.                                                        |
| 인덱스 콜렉션 | `Array`, `Int8Array`, ...                                                                                     | 인덱스 값으로 정렬된 데이터의 콜렉션을 나타냅니다. 배열 (형식 배열 포함) 과 배열형 객체를 포함합니다.              |

### 원시 타입과 래퍼 객체(Wrapper Object)

각 네이티브 객체는 각자의 프로퍼티와 메소드를 가집니다. static 프로퍼티, 메소드는 해당 인스턴스를 생성하지 않아도 사용할 수 있고 prototype에 속해있는 메소드는 해당 prototype을 상속 받은 인스턴스가 있어야만 사용할 수 있습니다.

그런데 원시 타입 값에 대해 표준 빌트인 객체의 메소드를 호출하면 정상적으로 동작합니다.

```javascript
var str = "Hello world!";
var res = str.toUpperCase();
console.log(res); // "HELLO WORLD!"
```

이는 원시 타입 값에 대해 표준 빌트인 객체의 메소드를 호출할 때, 원시 타입 값은 연관된 객체(래퍼 객체)로 일시 변환되기 때문에 가능한 것입니다. 메소드 호출이 종료되면 객체로 변환된 원시 타입 값은 다시 원시 타입 값으로 돌아갑니다.

래퍼 객체는 `String`, `Number`, `Boolean`이 있습니다.

<br>

## 2. 호스트 객체(Host Objects)

호스트 객체는 브라우저 환경에서 제공하는 `window`, `XmlHttpRequest`, `HTMLElement` 등의 DOM 노드 객체와 같이 호스트 환경에 정의된 객체를 말합니다. 브라우저에서 동작하는 환경과 브라우저 외부에서 동작하는 환경의 JavaScript는 다른 호스트 객체를 사용할 수 있습니다.

브라우저에서 동작하는 환경의 호스트 객체는 전역 객체 `window`, BOM, DOM, `XMLHttpRequest` 객체 등을 제공합니다.

### 전역 객체(Global Object)

전역 객체는 모든 객체의 최상위 객체를 의미하며 일반적으로 browser-side에서는 `window`, server-side(Node.js)에서는 `global` 객체를 의미합니다.

### BOM(Browser Object Model)

BOM은 브라우저 탭 또는 브라우저 창의 모델을 생성합니다. 최상위 객체는 `window` 객체로 현재 브라우저 창 또는 탭을 표현하는 객체입니다. 이 객체의 자식 객체들은 브라우저의 다른 기능들을 표현합니다. 이 객체들은 표준 빌트인 객체가 구성된 후에 구성됩니다.

<p align='center'>
    <img src='https://user-images.githubusercontent.com/22341362/139572121-310a654e-f31a-4be6-a4c7-ed5e034b113a.png' alt='BOM' width="300px">
</p>

### DOM(Document Object Model)

DOM은 현재 웹 페이지의 모델을 생성합니다. 최상위 객체는 `document` 객체로 전체 문서를 표현합니다. 이 객체의 자식 객체들은 문서의 다른 요소들을 표현합니다. 이 객체들은 표준 빌트인 객체가 구성된 후에 구성됩니다.

<p align='center'>
    <img src='https://user-images.githubusercontent.com/22341362/139572123-8b6a6ebd-081a-413b-8939-df93c4da653c.png' alt='DOM' width="300px">
</p>

<br>

## 3. 사용자 정의 객체(User-Defined Object)

<br>

## Reference

https://poiemaweb.com/js-built-in-object

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects
