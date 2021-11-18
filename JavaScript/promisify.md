# Promisify

콜백을 받는 함수를 promise를 반환하는 함수로 바꾸는 것을 "promisification"이라고 합니다.

새로운 함수 `loadScriptPromise(src)`는 `loadScript()`와 동일하게 작동하지만 `callback`을 제외한 `src`만 인수로 받아야 하고, promise를 반환해야 합니다.

```javascript
let loadScriptPromise = function (src) {
    return new Promise((resolve, reject) => {
        loadScript(src, (err, script) => {
            if (err) reject(err);
            else resolve(script);
        });
    });
};

// 사용법
loadScriptPromise('path/script.js').then(...);
```

함수 하나가 아닌 여러 개의 함수를 promise화 해야 할 때는 헬퍼 함수를 만드는게 좋습니다. Promise화를 적용할 함수 `f`를 받고 래퍼 함수를 반환하는 함수 `promisify(f)`를 만들어봅시다.

```javascript
function promisify(f) {
    // 래퍼 함수를 반환함
    return function (...args) {
        return new Promise((resolve, reject) => {
            // f에서 사용할 커스텀 콜백
            function callback(err, result) {
                if (err) {
                    reject(err);
                } else {
                    resolve(result);
                }
            }

            args.push(callback);

            f.call(this, ...args); // 기존 함수를 호출
        });
    };
}

// 사용법
let loadScriptPromise = promisify(loadScript);
loadScriptPromise(...).then(...);
```

위 예시는 promise화 할 함수가 인수가 두 개 `(err, result)`인 콜백을 받을 것이라 가정하고 작성되었습니다.

그런데 함수 `f`가 두 개를 초과하는 인수를 가진 콜백 `callback(err, res1, res2, ...)`를 받는다면 어떤 일이 발생할까요? 이런 경우를 대비하여 좀 더 진화한 `promisify()`를 만들어 봅시다. 새롭게 만든 함수를 `promisify(f, true)` 형태로 호출하면, promise 결과는 콜백의 성공 케이스(`results`)를 담은 배열 `[res1, res2, ...]`이 됩니다.

```javascript
function promisify(f, manyArgs = false) {
    return function (...args) {
        return new Promise((resolve, reject) => {
            function callback(err, ...results) {
                if (err) {
                    reject(err);
                } else {
                    resolve(manyArgs ? results : results[0]);
                }
            }

            args.push(callback);

            f.call(this, ...args);
        });
    };
}

// 사용법
f = promisify(f, true);
f(...).then(arrayOfResults => ..., err => ...);
```

`callback(result)`같이 `err`이 없는 형태나 지금까지 언급하지 않은 형태의 이색적인 콜백도 있을 수 있는데, 이런 경우엔 헬퍼 함수를 사용하지 않고 직접 promise화 하면 됩니다.

es6-promisify 모듈이나 Node.js 내장 함수 `util.promisify()`를 사용해 promise화 할 수 있습니다.

<br>

## Reference

https://ko.javascript.info/promisify
