# 호이스팅(Hoisting)

JavaScript에서 호이스팅이란 인터프리터가 변수와 함수의 메모리 공간을 선언 전에 미리 할당하는 것을 의미합니다. `var`로 선언한 변수의 경우, 호이스팅 시 `undefined`로 변수를 초기화합니다. 반면, `let`과 `const`로 선언한 변수의 경우, 호이스팅 시 변수를 초기화하지 않습니다.

호이스팅을 설명할 때, 주로 "변수의 선언과 초기화를 분리한 후, 선언만 코드의 최상단으로 옮기는 것"으로 말합니다. 따라서 변수를 정의하는 코드보다 사용하는 코드가 앞서 등장할 수 있습니다. 다만, 선언과 초기화를 함께 수행하는 경우, 선언 코드까지 실행해야 변수가 초기화된 상태가 됨을 주의해야 합니다.

### 예제

JavaScript는 함수의 코드를 실행하기 전에 함수 선언에 대한 메모리부터 할당합니다. 덕분에 함수를 호출하는 코드를 함수 선언보다 앞서 배치할 수 있습니다.

```javascript
// 함수 선언 후 실행
function catName(name) {
    console.log(`제 고양이의 이름은 ${name}입니다.`);
}

catName("호랑이");

// 호이스팅으로 인해, 함수 실행 후 선언이 가능
catName("호랑이");

function catName(name) {
    console.log(`제 고양이의 이름은 ${name}입니다.`);
}
```

<br>

## 호이스팅의 대상

JavaScript는 초기화를 제외한 선언만 호이스팅합니다. 변수를 먼저 사용하고 그 후에 선언 및 초기화가 나타나면, 사용하는 시점의 변수는 기본 초기화 상태 (`var` 선언 시 `undefined`, 그 외에는 초기화하지 않음) 입니다.

```javascript
console.log(num); // 호이스팅한 var 선언으로 인해 undefined 출력
var num; // 선언
num = 6; // 초기화
```

아래 예제는 선언 없이 초기화만 존재합니다. 따라서 호이스팅도 없고, 변수를 읽으려는 시도에서는 `ReferenceError` 예외가 발생합니다.

```javascript
console.log(num); // ReferenceError
num = 6; // 초기화
```

변수 초기화는 변수 선언이 되지 않은 경우 변수 선언까지 병행하므로 변수를 사용할 수 있습니다. 그러나 변수를 명시적으로 선언하지 않았기 때문에 호이스팅은 발생하지 않습니다.

```javascript
console.log(a); // ReferenceError
a = 1;
console.log(a, b); // 1 undefined
var b = 2;
```

<br>

## `let`과 `const` 호이스팅

`let`과 `const`로 선언한 변수도 호이스팅 대상이지만, `var`과 달리 호이스팅 시 `undfined`로 변수를 초기화하지는 않습니다. 따라서 변수를 초기화하기 전에 변수를 읽는 코드가 먼저 나타나면 `ReferenceError`가 발생합니다.

<br>

## Reference

https://developer.mozilla.org/ko/docs/Glossary/Hoisting
