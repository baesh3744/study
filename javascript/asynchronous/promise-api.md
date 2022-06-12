# Promise API

## `Promise.all()`

```javascript
let promise = Promise.all([...promises...]);
```

`Promise.all()`은 요소 전체가 promise인 배열 (엄밀히 따지면 iterable object이지만, 대개는 배열) 을 받고 새로운 promise를 반환합니다.

배열 안 promise가 모두 처리되면 새로운 promise가 이행되는데, 배열 안 promise의 결과값을 담은 새로운 promise의 `result`가 됩니다.

```javascript
Promise.all([
    new Promise((resolve) => setTimeout(() => resolve(1), 3000)), // 1
    new Promise((resolve) => setTimeout(() => resolve(2), 2000)), // 2
    new Promise((resolve) => setTimeout(() => resolve(3), 1000)), // 3
]).then(console.log); // [1, 2, 3]
```

배열 `result`의 요소 순서는 `Promise.all()`에 전달되는 promise 순서와 상응합니다. 위 예시에서 첫 번째 promise가 가장 늦게 이행되더라도 처리 결과는 배열의 첫 번째 요소에 저장됩니다.

작업해야 할 데이터가 담긴 배열을 promise 배열로 매핑하고, 이 배열을 `Promise.all()`로 감싸는 트릭은 자주 사용됩니다.

URL이 담긴 배열을 `fetch()`를 써서 처리하는 예시를 살펴봅시다.

```javascript
let urls = [
    "https://api.github.com/users/iliakan",
    "https://api.github.com/users/remy",
    "https://api.github.com/users/jeresig",
];

// fetch를 사용해 url을 promise로 매핑합니다.
let requests = urls.map((url) => fetch(url));

Promise.all(requests).then((responses) =>
    responses.forEach((response) =>
        console.log(`${response.url}: ${response.status}`)
    )
);
```

`Promise.all()`에 전달되는 promise 중 하나라도 거부되면, `Promise.all()`이 반환하는 promise는 에러와 함께 바로 거부됩니다.

```javascript
Promise.all([
    new Promise((resolve, reject) => setTimeout(() => resolve(1), 3000)), // 1
    new Promise((resolve, reject) =>
        setTimeout(() => reject(new Error("에러 발생!")), 2000)
    ), // 2
    new Promise((resolve, reject) => setTimeout(() => resolve(3), 1000)), // 3
]).then(console.log); // Error: 에러 발생!
```

Iterable object가 아닌 "일반" 값도 `Promise.all(iterable)`에 넘길 수 있습니다.

```javascript
Promise.all([
    new Promise((resolve, reject) => {
        setTimeout(() => resolve(1), 1000);
    }),
    2,
    3,
]).then(console.log); // [1, 2, 3]
```

<br>

## `Promise.allSettled()`

`Promise.all()`은 promise가 하나라도 거절되면 전체를 거절합니다. 따라서, promise 결과가 모두 필요할 때같이 "모 아니면 도"일 때 유용합니다.

반면, `Promise.allSettled()`는 모든 promise가 처리될 때까지 기다리지만, 반환되는 배열은 다음과 같은 요소를 갖습니다.

-   응답이 성공할 경우 - `{ status: "fulfilled", value: result }`
-   에러가 발생한 경우 - `{ status: "rejected", reason: error }`

```javascript
let urls = [
    "https://api.github.com/users/iliakan",
    "https://api.github.com/users/remy",
    "https://no-such-url",
];

Promise.allSettled(urls.map((url) => fetch(url))).then((results) => {
    results.forEach((result, num) => {
        if (result.status == "fulfilled") {
            console.log(`${urls[num]}: ${result.value.status}`);
        }
        if (result.status == "rejected") {
            console.log(`${urls[num]}: ${result.reason}`);
        }
    });
});

// results = [
//     { status: 'fulfilled', value: ...응답... },
//     { status: 'fulfilled', value: ...응답... },
//     { status: 'rejected', reason: ...에러 객체... },
// ]
```

<br>

### Polyfill

브라우저가 `Promise.allSettled()`를 지원하지 않는다면 polyfill을 구현하면 됩니다.

```javascript
if (!Promise.allSettled) {
    Promise.allSettled = function (promises) {
        return Promise.all(
            promises.map((p) =>
                Promise.resolve(p).then(
                    (value) => ({
                        status: "fulfilled",
                        value,
                    }),
                    (reason) => ({
                        status: "rejected",
                        reason,
                    })
                )
            )
        );
    };
}
```

여기서 `promises.map()`은 입력값을 받아 `p => Promise.resolve(p)`로 입력값을 promise로 변화시킵니다. 그리고 모든 promise에 `.then()` 핸들러가 추가됩니다. `.then()` 핸들러는 성공한 promise의 결과값 `value`를 `{ status: 'fulfilled', value }`로, 실패한 promise의 결과값 `reason`을 `{ status: 'rejected', reason }`으로 변경합니다.

<br>

## `Promise.race()`

```javascript
let promise = Promise.race(iterable);
```

`Promise.race()`는 `Promise.all()`과 비슷하지만, 가장 먼저 처리되는 promise의 결과 (혹은 에러) 를 반환합니다.

```javascript
Promise.race([
    new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
    new Promise((resolve, reject) =>
        setTimeout(() => reject(new Error("에러 발생!")), 2000)
    ),
    new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000)),
]).then(console.log); // 1
```

위 예시에서 첫 번째 promise가 가장 빨리 처리상태가 되기 때문에 첫 번째 promise의 결과가 `result` 값이 됩니다. 이렇게 `Promise.race()`를 사용하면 "경주(race)의 승자"가 나타난 순간 다른 promise의 결과 또는 에러는 무시됩니다.

<br>

## `Promise.resolve()` / `Promise.reject()`

`Promise.resolve()`와 `Promise.reject()`는 `async/await` 문법이 생긴 후로 쓸모없어졌기 때문에 근래에는 거의 사용하지 않습니다.

### `Promise.resolve()`

`Promise.resolve(value)`는 결과값이 `value`인 이행 상태 promise를 생성합니다.

아래 코드와 동일한 일을 수행합니다.

```javascript
let promise = new Promise((resolve) => resolve(value));
```

`Promise.resolve()`는 호환성을 위해 함수가 promise를 반환하도록 해야 할 때 사용할 수 있습니다.

아래 예시에서 함수 `loadCached()`는 인수로 받은 URL을 대상으로 `fetch()`를 호출하고, 그 결과를 기억(cache)합니다. 나중에 동일한 URL을 대상으로 `fetch()`를 호출하면 캐시에서 호출 결과를 즉시 가져오는데, 이때 `Promise.resolve()`를 사용해 캐시 된 내용을 promise로 만들어 반환 값이 항상 promise가 되게 합니다.

```javascript
let cache = new Map();

function loadCached(url) {
    if (cache.has(url)) {
        return Promise.resolve(cache.get(url));
    }

    return fetch(url)
        .then((response) => response.text())
        .then((text) => {
            cache.set(url, text);
            return text;
        });
}
```

`loadCached()`를 호출하면 promise가 반환된다는 것이 보장되기 때문에 `loadCached(url).then(...)`을 사용할 수 있습니다.

### `Promise.reject()`

`Promise.reject(error)`는 결과값이 `error`인 거부 상태 promise를 생성합니다.

아래 코드와 동일한 일을 수행합니다.

```javascript
let promise = new Promise((resolve, reject) => reject(error));
```

<br>

## Reference

https://ko.javascript.info/promise-api
