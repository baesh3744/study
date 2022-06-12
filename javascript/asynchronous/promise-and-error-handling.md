# Promise와 에러 핸들링

Promise가 거부되면 제어 흐름이 제일 가까운 rejection 핸들러로 넘어가기 때문에 promise 체인을 사용하면 에러를 쉽게 처리할 수 있습니다.

### 예시

아래 예시에서는 존재하지 않는 주소를 `fetch()`에 넘겨주고, `.catch()`에서 에러를 처리합니다.

```javascript
fetch("https://no-such-server.blabla")
    .then((response) => response.json())
    .catch((err) => console.log(err));
```

예시에서 보듯 `.catch()`는 첫 번째 핸들러일 필요가 없고 하나 혹은 여러 개의 `.then()` 뒤에 올 수 있습니다.

정상적인 경우라면 `.catch()`는 절대 트리거 되지 않습니다. 그런데 네트워크 문제, 잘못된 형식의 JSON 등으로 인해 위쪽 promise 중 하나라도 거부되면 `.catch()`에서 에러를 잡게 됩니다.

<br>

## 암시적 `try..catch`

Promise executor와 promise 핸들러 코드 주위엔 "보이지 않는(암시적) `try..catch`"가 있습니다. 예외가 발생하면 암시적 `try..catch`에서 예외를 잡고 이를 reject처럼 다룹니다.

```javascript
new Promise((resolve, reject) => {
    throw new Error("에러 발생!");
}).catch(console.log); // Error: 에러 발생!

// 위 예시는 아래와 똑같이 동작합니다.
new Promise((resolve, reject) => {
    reject(new Error("에러 발생!"));
}).catch(console.log); // Error: 에러 발생!
```

Executor 주위의 "암시적 `try..catch`"는 스스로 에러를 잡고, 에러를 거부상태의 promise로 변경시킵니다.

이런 일은 executor 함수뿐만 아니라 핸들러에서도 발생합니다. `.then()` 핸들러 안에서 `throw`를 사용해 에러를 던지면, 이 자체가 거부된 promise를 의미하게 됩니다. 따라서 제어 흐름이 가장 가까운 에러 핸들러로 넘어갑니다.

```javascript
new Promise((resolve, reject) => {
    resolve("ok");
})
    .then((result) => {
        throw new Error("에러 발생!");
    })
    .catch(console.log); // Error: 에러 발생!
```

`throw`문이 만든 에러뿐만 아니라 모든 종류의 에러가 암시적 `try..catch`에서 처리됩니다.

```javascript
new Promise((reolve, reject) => {
    resolve("ok");
})
    .then((result) => {
        blabla(); // 존재하지 않는 함수
    })
    .catch(console.log); // ReferenceError: blabla is not defined
```

마지막 `.catch()`는 이렇게 명시적인 거부뿐만 아니라 핸들러 위쪽에서 발생한 비정상 에러 또한 잡습니다.

<br>

## 다시 던지기

체인 마지막의 `.catch()`는 `try..catch`와 유사한 역할을 합니다. `.then()` 핸들러를 원하는 만큼 사용하다 마지막에 `.catch()` 하나만 붙이면 `.then()` 핸들러에서 발생한 모든 에러를 처리할 수 있습니다.

일반 `try..catch`에서는 에러를 분석하고, 처리할 수 없는 에러라 판단되면 에러를 다시 던질 때가 있습니다. Promise에서도 유사한 일을 할 수 있습니다.

`.catch()` 안에서 `throw`를 사용하면 제어 흐름이 가장 가까운 곳에 있는 에러 핸들러로 넘어갑니다. 여기서 에러가 성공적으로 처리되면 가장 가까운 곳에 있는 `.then()` 핸들러로 제어 흐름이 넘어가 실행이 이어집니다.

```javascript
// 에러를 성공적으로 처리. catch -> then
new Promise((resolve, reject) => {
    throw new Error("에러 발생!");
})
    .catch(function (error) {
        console.log("에러가 잘 처리되었습니다. 정상적으로 실행이 이어집니다.");
    })
    .then(() => console.log("다음 핸들러가 실행됩니다."));

// 에러를 처리하지 못함. catch -> catch
new Promise((resolve, reject) => {
    throw new Error("에러 발생!");
})
    .catch(function (error) {
        if (error instanceof URIError) {
            // 에러 처리
        } else {
            console.log("처리할 수 없는 에러");

            throw error; // 에러 다시 던지기
        }
    })
    .then(function () {
        /* 여기는 실행되지 않습니다. */
    })
    .catch((error) => {
        console.log(`알 수 없는 에러가 발생함: ${error}`);
    });
```

<br>

## 처리되지 못한 거부

에러를 처리하지 못하면 무슨 일이 생길까요? 아래 예시처럼 체인 끝에 `.catch()`를 추가하지 못하는 경우처럼 말이죠.

```javascript
new Promise(function () {
    noSuchFunction(); // 에러 (존재하지 않는 함수)
}).then(() => {
    // 성공 상태의 promise를 처리하는 핸들러
}); // 끝에 .catch가 없음
```

에러가 발생하면 promise는 거부상태가 되고, 실행 흐름은 가장 가까운 rejection 핸들러로 넘어갑니다. 그런데 위 예시엔 예외를 처리해 줄 핸들러가 없어서 에러가 "갇혀버립니다".

일반적인 에러가 발생하고 이를 `try..catch`로 처리하지 못하는 경우, 스크립트가 죽고 콘솔 창에 메시지가 출력됩니다. 거부된 promise를 처리하지 못했을 때도 유사한 일이 발생합니다. JavaScript 엔진은 promise 거부를 추적하다가 위와 같은 상황이 발생하면 전역 에러를 생성합니다. 브라우저 환경에서는 이런 에러를 `unhandledrejection` 이벤트로 잡을 수 있습니다.

```javascript
window.addEventListener("unhandledrejection", function (event) {
    console.log(event.promise); // [object Promise] - 에러를 생성하는 promise
    console.log(event.reason); // Error: 에러 발생! - 처리하지 못한 에러 객체
});
```

`unhandledrejection` 이벤트는 HTML 명세서에 정의된 표준 이벤트입니다. 브라우저 환경에서는 에러가 발생했는데 `.catch()`가 없으면 `unhandledrejection` 핸들러가 트리거됩니다. `unhandledrejection` 핸들러는 에러 정보가 담긴 `event` 객체를 받기 때문에 이 핸들러 안에서 원하는 작업을 할 수 있습니다.

<br>

## Reference

https://ko.javascript.info/promise-error-handling
