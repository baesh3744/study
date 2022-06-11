# Promise

1. "제작 코드(producing code)"는 원격에서 스크립트를 불러오는 것과 같이 시간이 걸리는 일을 합니다.
2. "소비 코드(consuming code)"는 "제작 코드"의 결과를 기다렸다가 이를 소비합니다. 이때 소비의 주체(함수)는 여럿이 될 수 있습니다.
3. "Promise"는 "제작 코드"와 "소비 코드"를 연결해 주는 특별한 JavaScript 객체입니다. "Promise"는 시간이 얼마나 걸리든 상관없이 약속한 결과를 만들어 내는 "제작 코드"가 준비되었을 때, 모든 "소비 코드"가 결과를 사용할 수 있도록 해줍니다.

<br>

## `promise` 객체

`promise` 객체는 아래와 같은 문법으로 만들 수 있습니다.

```javascript
let promise = new Promise(function (resolve, reject) {
    // ...
});
```

`new Promise()`에 전달되는 함수는 `executor(실행자, 실행 함수)`라고 부릅니다. `executor()`는 `new Promise()`가 만들어질 때 자동으로 실행되는데, 결과를 최종적으로 만들어내는 제작 코드를 포함합니다.

`executor()`의 인수 `resolve`와 `reject`는 JavaScript에서 자체 제공하는 콜백입니다. 개발자는 `resolve`와 `reject`를 신경 쓰지 않고 `executor()` 안 코드만 작성하면 됩니다.

대신 `executor()`는 결과를 즉시 얻든 늦게 얻든 상관없이 상황에 따라 인수로 넘겨준 콜백 중 하나를 반드시 호출해야 합니다.

-   `resolve(value)` - 동작이 성공적으로 끝난 경우 그 결과를 나타내는 `value`와 함께 호출
-   `reject(error)` - 에러 발생 시 에러 객체를 나타내는 `error`와 함께 호출

요약하면 다음과 같습니다. `executor()`는 자동으로 실행되는데 여기서 원하는 일이 처리됩니다. 처리가 끝나면 `executor()`는 처리 성공 여부에 따라 `resolve`나 `reject`를 호출합니다.

한편, `new Promise()` 생성자가 반환하는 `promise` 객체는 다음과 같은 내부 프로퍼티를 갖습니다.

-   `state` - 처음엔 `"pending"`이었다가 `resolve`가 호출되면 `"fulfilled"`, `reject`가 호출되면 `"rejected"`로 변합니다.
-   `result` - 처음엔 `undefined`이었다가 `resolve(value)`가 호출되면 `value`, `reject(error)`가 호출되면 `error`로 변합니다.

<br>

## 소비 함수: `.then()`, `.catch()`, `.finally()`

### `.then()`

`.then()`의 문법은 다음과 같습니다.

```javascript
promise.then(
    function (result) {
        /* 결과(result)를 다룹니다 */
    },
    function (error) {
        /* 에러(error)를 다룹니다 */
    }
);
```

`.then()`의 첫 번째 인수는 promise가 이행되었을 때 실행되는 함수이고, 여기서 실행 결과를 받습니다.

`.then()`의 두 번쨰 인수는 promise가 거부되었을 때 실행되는 함수이고, 여기서 에러를 받습니다.

동작이 성공적으로 처리된 경우만 다루고 싶다면 `.then()`에 인수를 하나만 전달하면 됩니다.

```javascript
// 1. Promise가 성공적으로 이행된 경우
let promise = new Promise(function (resolve, reject) {
    setTimeout(() => resolve("done!"), 1000);
});

// resolve() 함수는 .then()의 첫 번째 함수(인수)를 실행합니다.
promise.then(
    (result) => console.log(result), // 1초 후 "done!"을 출력합니다.
    (error) => console.log(error) // 실행되지 않습니다.
);

// 2. Promise가 거부된 경우
let promise = new Promise(function (resolve, reject) {
    setTimeout(() => reject(new Error("에러 발생!")), 1000);
});

// reject() 함수는 .then()의 두 번째 함수를 실행합니다.
promise.then(
    (result) => console.log(result), // 실행되지 않습니다.
    (error) => console.log(error) // 1초 후 "Error: 에러 발생!"을 출력합니다.
);

// 3. 동작이 성공적으로 처리된 경우만 다루고 싶은 경우
let promise = new Promise(function (resolve, reject) {
    setTimeout(() => resolve("done!"), 1000);
});

promise.then(alert); // 1초 후 "done!"을 출력합니다.
```

### `.catch()`

에러가 발생한 경우만 다루고 싶다면 `.then(null, errorHandlingFunction)`같이 `null`을 첫 번째 인수로 전달하면 됩니다. `.catch(errorHandlingFunction)`을 써도 되는데, `.catch()`는 `.then()`에 첫 번째 인수로 `null`을 전달하는 것과 동일하게 동작합니다. `.catch(f)`는 문법이 간결하다는 점만 빼고, `.then(null, f)`와 완벽하게 같습니다.

```javascript
let promise = new Promise(function (resolve, reject) {
    setTimeout(() => reject(new Error("에러 발생!")), 1000);
});

promise.catch(alert); // 1초 후 "Error: 에러 발생!"을 출력합니다.
```

### `.finally()`

`try { ... } catch { ... }`에 finally절이 있는 것처럼, promise에도 `.finally()`가 있습니다.

Promise가 처리되면 (이행이나 거부) `f`가 항상 실행된다는 점에서 `.finally(f)` 호출은 `.then(f, f)`와 유사합니다.

쓸모가 없어진 로딩 인디케이터(loading indicator)를 멈추는 경우같이 결과가 어떻든 마무리가 필요하면 `.finally()`가 유용합니다.

사용법은 아래와 같습니다.

```javascript
new Promise((resolve, reject) => {
    // ...
})
    .finally(() => {
        /* 로딩 인디케이터 중지 */
    }) // 성공, 실패 여부와 상관없이 promise가 처리되면 실행됨
    .then();
```

그런데 `.finally(f)`는 `.then(f, f)`와 완전히 같지는 않습니다. 차이점은 다음과 같습니다.

1. `.finally()` 핸들러에는 인수가 없습니다. `.finally()`에서는 promise가 이행되었는지 거부되었는지 알 수 없습니다. `.finally()`에서는 절차를 마무리하는 "보편적" 동작을 수행하기 때문에 성공, 실패 여부를 몰라도 됩니다.
2. `.finally()` 핸들러는 자동으로 다음 핸들러에 결과와 에러를 전달합니다. `.finally()`는 promise 결과를 처리하기 위해 만들어 진 게 아닙니다. Promise 결과는 `.finally()`를 통과해서 전달됩니다.
3. `.finally(f)`는 함수 `f`를 중복해서 쓸 필요가 없기 때문에 `.then(f, f)`보다 문법 측면에서 더 편리합니다.

<br>

## 예시: `loadScript()`

```javascript
// 콜백 기반으로 작성한 함수
function loadScript(src, callback) {
    let script = document.createElement("script");
    script.src = src;

    srcipt.onload = () => callback(null, script);
    script.onerror = () =>
        callback(new Error(`${src}를 불러오는 도중에 에러가 발생했습니다.`));

    document.head.append(script);
}

// Promise를 이용해 작성한 함수
function loadScript(src) {
    return new Promise(function (resolve, reject) {
        let script = document.createElement("script");
        script.src = src;

        srcipt.onload = () => resolve(script);
        script.onerror = () =>
            reject(new Error(`${src}를 불러오는 도중에 에러가 발생했습니다.`));

        document.head.append(script);
    });
}

let promise = loadScript("/my/script.js");

promise.then(
    (script) => console.log(`${script.src}를 불러왔습니다!`),
    (error) => console.log(`Error: ${error.message}`)
);

promise.then((script) => console.log("또다른 핸들러..."));
```

<br>

## Promise vs. Callback

| Promise                                                                                                                                                       | Callback                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Promise를 이용하면 흐름이 자연스럽습니다. `loadScript(script)`로 스크립트를 읽고, 결과에 따라 그 다음(`.then()`)에 무엇을 할지에 대한 코드를 작성하면 됩니다. | `loadScript(script, callback)`을 호출할 때, 함께 호출할 `callback()` 함수가 준비되어 있어야 합니다. `loadScript()`를 호출하기 이전에 호출 결과로 무엇을 할지 미리 알고 있어야 합니다. |
| Promise에 원하는 만큼 `.then()`을 호출할 수 있습니다.                                                                                                         | Callback은 하나만 가능합니다.                                                                                                                                                         |

<br>

## Reference

https://ko.javascript.info/promise-basics
