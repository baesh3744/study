# 1급 객체(First-Class Object)

## 1급 시민(First-Class Citizen)

1급 시민이란 다른 요소에서 일반적으로 지원하는 연산을 모두 지원하는 요소를 말합니다. 보통 인자로 전달하기, 함수에서 반환하기, 수정하기, 변수에 할당하기를 지원할 때 1급 시민이라고 합니다.

<br>

## 1급 객체(First-Class Object)

1급 객체란 1급 시민의 조건을 충족하는 객체를 말합니다. Robin Popplestone이라는 분이 제시한 1급 객체의 조건은 아래와 같습니다.

1. 모든 1급 객체는 함수의 실질적인 매개변수가 될 수 있습니다.
2. 모든 1급 객체는 함수의 반환값이 될 수 있습니다.
3. 모든 1급 객체는 할당의 대상이 될 수 있습니다.
4. 모든 1급 객체는 비교 연산 (==, equal) 을 적용할 수 있습니다.

<br>

## 1급 함수(First-Class Function)

1급 함수란 1급 시민의 조건과 익명 함수 (함수 리터럴) 을 지원하는 함수를 의미합니다. 1급 함수를 지원하는 언어에서 함수의 이름은 특별한 상태를 가지는 것이 아니라, 함수 타입을 가지는 변수로서 동작합니다.

JavaScript에서 함수는 1급 객체 즉, 1급 함수로서 동작합니다.

```javascript
// 1. 함수를 함수 리터럴로 생성할 수 있습니다.
// 2. 함수를 변수에 저장할 수 있습니다.
const increase = function (num) {
    return ++num;
};

const decrease = function (num) {
    return --num;
};

// 2. 함수를 객체에 저장할 수 있습니다.
const predicates = { increase, decrease };

console.log(predicates);
// predicates: {
//     increase: [Function: increase],
//     decrease: [Function: decrease]
// }

// 3. 함수를 함수의 매개변수로 전달할 수 있습니다.
// 4. 함수를 함수의 반환값으로 사용할 수 있습니다.
function makeCounter(predicate) {
    let num = 0;

    return function () {
        num = predicate(num);
        return num;
    };
}

// 3. 함수를 함수의 매개변수로 전달할 수 있습니다.
const increaser = makeCounter(predicates.increase);
console.log(increaser); // 1
console.log(increaser); // 2
```

함수가 1급 객체로서 동작한다는 것은 고차 함수(HOF)의 지원을 의미하기 때문에 1급 함수의 지원은 함수형 프로그래밍의 필수 요소입니다.

<br>

## Reference

-   [junh0328/prepare_frontend_interview - "함수와 일급 객체"](https://github.com/junh0328/prepare_frontend_interview/blob/main/JS.md#%ED%95%A8%EC%88%98%EC%99%80-%EC%9D%BC%EA%B8%89-%EA%B0%9D%EC%B2%B4)
-   [Wikipedia - "First-class citizen"](https://en.wikipedia.org/wiki/First-class_citizen)
-   [Wikipedia - "First-class funciton"](https://en.wikipedia.org/wiki/First-class_function)
-   [Rhyno's DevLife Log - "1급 객체(first-class object)란?"](<https://jcsoohwancho.github.io/2019-10-18-1%EA%B8%89-%EA%B0%9D%EC%B2%B4(first-class-object)%EC%9D%B4%EB%9E%80/>)
