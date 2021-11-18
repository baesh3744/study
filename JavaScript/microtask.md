# 마이크로태스크(Microtask)

Promise 핸들러 `.then/catch/finally`는 항상 비동기적으로 실행됩니다. Promise가 즉시 이행되더라도 `.then/catch/finally` 아래에 있는 코드는 이 핸들러들이 실행되기 전에 실행됩니다.

```javascript
let promise = Promise.resolve();

promise.then(() => console.log("프라미스 성공!"));

console.log("코드 종료"); // 이 출력이 먼저 나타납니다.
```

<br>

## 마이크로태스크 큐

비동기 작업을 처리하려면 적절한 관리가 필요합니다. 이를 위해 ECMA에서는 `PromiseJobs`라는 내부 큐(internal queue)를 명시합니다. V8 엔진에서는 이를 "마이크로태스크 큐(microtask queue)"라고 부르기 때문에 이 용어가 좀 더 선호됩니다.

명세서의 설명을 살펴봅시다.

-   마이크로태스크 큐는 먼저 들어온 작업을 먼저 실행합니다. (FIFO, First-In-First-Out)
-   실행할 것이 아무것도 남아있지 않을 때만 마이크로태스크 큐에 있는 작업이 실행되기 시작합니다.

요약하자면, 어떤 promise가 준비되었을 때 이 promise의 `.then/catch/finally` 핸들러가 큐에 들어간다고 생각하시면 됩니다. 이때 핸들러들은 여전히 실행되지 않습니다. 현재 코드에서 자유로운 상태가 되었을 때에서야 JavaScript 엔진은 큐에서 작업을 꺼내 실행합니다.

Promsie 핸들러는 항상 내부 큐를 통과하게 됩니다.

여러 개의 `.then/catch/finally`를 사용해 만든 체인의 경우, 각 핸들러는 비동기적으로 실행됩니다. 큐에 들어간 핸들러 각각은 현재 코드가 완료되고, 큐에 적제된 이전 핸들러의 실행이 완료되었을 때 실행됩니다.

그렇다면 "프라미스 성공!"을 먼저, "코드 종료"를 나중에 출력되게 하려면 어떻게 해야 할까요? 실행 순서가 중요한 경우에는 이런 요구사항이 충족되도록 코드를 작성해야 합니다.

방법은 아주 쉽습니다. `.then()`을 사용해 큐에 넣으면 됩니다.

```javascript
Promise.resolve()
    .then(() => console.log("프라미스 성공!"))
    .then(() => console.log("코드 종료"));
```

<br>

## 처리되지 못한 거부

이제 JavaScript 엔진이 어떻게 처리되지 못한 거부(`unhandledrejection`)를 찾는지 정확히 알 수 있습니다.

"처리되지 못한 거부"는 마이크로태스크 큐 끝에서 Promise 에러가 처리되지 못할 때 발생합니다.

정상적인 경우라면 개발자는 에러가 생길 것을 대비하여 promise 체인에 `.catch()`를 추가해 에러를 처리합니다.

```javascript
let promise = Promsie.reject(new Error("프라미스 실패!"));
promise.catch((err) => console.log("잡았다!"));

// 에러가 잘 처리되었으므로 실행되지 않습니다.
window.addEventListener("unhandledrejection", (event) =>
    console.log(event.reason)
);
```

그런데 `.catch()`를 추가해주는 걸 잊은 경우, 엔진은 마이크로태스크 큐가 빈 이후에 `unhandledrejection` 이벤트를 트리거합니다.

```javascript
let promise = Promsie.reject(new Error("프라미스 실패!"));

// 프라미스 실패!
window.addEventListener("unhandledrejection", (event) =>
    console.log(event.reason)
);
```

그런데 만약 아래와 같이 `setTimeout()`을 이용해 에러를 나중에 처리하면 어떤 일이 생길까요?

```javascript
let promise = Promsie.reject(new Error("프라미스 실패!"));
setTimeout(() => promise.catch((err) => console.log("잡았다!")));

// 프라미스 실패!
window.addEventListener("unhandledrejection", (event) =>
    console.log(event.reason)
);
```

예시를 실행하면 "프라미스 실패!"가 먼저, "잡았다!"가 나중에 출력되는 걸 확인할 수 있습니다.

`unhandledrejection`은 마이크로태스크 큐에 있는 작업 모두가 완료되었을 때 생성됩니다. 엔진은 promise들을 검사하고 이 중 하나라도 "거부(rejected)" 상태이면 `unhandledrejection` 핸들러를 트리거하죠. `.catch()`는 `unhandledrejection`이 발생한 이후에 트리거되므로 "프라미스 실패!"가 출력됩니다.

<br>

## Reference

https://ko.javascript.info/microtask-queue
