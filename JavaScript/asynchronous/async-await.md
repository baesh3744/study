# async와 await

## `async`

`async`는 function 앞에 위치합니다. function 앞에 `async`를 붙이면 해당 함수는 항상 promise를 반환합니다. Promise가 아닌 값을 반환하더라도 이행 상태의 promise(resolved promise)로 값을 감싸 이행된 promise가 반환되도록 합니다.

아래 예시의 함수를 호출하면 `result`가 `1`인 이행 promise가 반환됩니다.

```javascript
async function f() {
    return 1;
}

f().then(console.log); // 1
```

명시적으로 promise를 반환하는 것도 가능한데, 결과는 동일합니다.

```javascript
async function f() {
    return Promise.resolve(1);
}

f().then(console.log); // 1
```

`async`가 붙은 함수는 반드시 promise를 반환하고, promise가 아닌 것은 promise로 감싸 반환합니다.

<br>

## `await`

`await`는 `async` 함수 안에서만 동작합니다.

JavaScript는 `await` 키워드를 만나면 promise가 처리(settled)될 때까지 기다립니다. 결과는 그 이후 반환됩니다.

```javascript
async function f() {
    let promise = new Promise((resolve, reject) => {
        setTimeout(() => resolve("완료!"), 1000);
    });

    let result = await promise; // (*) Promise가 이행될 때까지 기다립니다.

    console.log(result); // 완료!
}

f();
```

함수를 호출하고 함수 본문이 실행되는 도중에 `(*)`로 표시한 줄에서 실행이 잠시 "중단"되었다가 promise가 처리되면 실행이 재개됩니다. 이때 promise 객체의 `result` 값이 변수 result에 할당됩니다. 따라서 위 예시를 실행하면 1초 뒤에 "완료!"가 출력됩니다.

`await`는 말 그대로 promise가 처리될 때까지 함수 실행을 기다리게 만듭니다. Promise가 처리되면 그 결과와 함께 실행이 재개됩니다. Promise가 처리되길 기다리는 동안에는 엔진이 다른 일 (다른 스크립트 실행, 이벤트 처리 등) 을 할 수 있기 때문에 CPU 리소스가 낭비되지 않습니다.

`await`는 `promise.then()`보다 좀 더 세련되게 promise의 `result` 값을 얻을 수 있도록 해주는 문법입니다. `promise.then()`보다 가독성 좋고 쓰기도 쉽습니다.

<br>

## 에러 핸들링

Promise가 정상적으로 이행되면 `await promise`는 promise 객체의 `result`에 저장된 값을 반환합니다. 반면 promise가 거부되면 마치 `throw`문을 작성한 것처럼 에러가 던져집니다.

```javascript
async function f() {
    await Promise.reject(new Error("에러 발생!"));
}
```

위 코드는 아래 코드와 동일합니다.

```javascript
async function f() {
    throw new Error("에러 발생!");
}
```

실제 상황에서는 promise가 거부되기 전에 약간의 시간이 지체되는 경우가 있습니다. 이런 경우엔 `await`가 에러를 던지기 전에 지연이 발생합니다.

`await`가 던진 에러는 `throw`가 던진 에러를 잡을 때처럼 `try..catch`를 사용해 잡을 수 있습니다.

```javascript
async function f() {
    try {
        let response = await fetch("http://유효하지-않은-주소");
    } catch (err) {
        console.log(err); // TypeError: failed to fetch
    }
}

f();
```

에러가 발생하면 제어 흐름이 `catch` 블록으로 넘어갑니다. 여러 줄의 코드를 `try`로 감싸는 것도 가능합니다.

`try..catch`가 없으면 아래 예시의 async 함수 `f()`를 호출해 만든 promise가 거부 상태가 됩니다. `f()`에 `.catch()`를 추가하면 거부된 promise를 처리할 수 있습니다.

```javascript
async function f() {
    let response = await fetch("http://유효하지-않은-주소");
}

f().catch(console.log); // TypeError: failt to fetch
```

`.catch()`를 추가하는 걸 잊으면, 처리되지 않은 promise 에러가 발생합니다. 이런 에러는 전역 이벤트 핸들러 `unhandledrejection`을 사용해 잡을 수 있습니다.

<br>

## Reference

https://ko.javascript.info/async-await
