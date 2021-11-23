# async 이터레이터와 제너레이터

비동기 이터레이터(asynchronous iterator)를 사용하면 비동기적으로 들어오는 데이터를 필요에 따라 처리할 수 있습니다. 네트워크를 통해 데이터가 여러 번에 걸쳐 들어오는 상황을 처리할 수 있게 됩니다. 비동기 이터레이터에 더하여 비동기 제너레이터(asynchronous generator)를 사용하면 이런 데이터를 좀 더 편리하게 처리할 수 있습니다.

<br>

## async 이터레이터

"일반" 이터러블은 객체입니다.

```javascript
let range = {
    from: 1,
    to: 5,

    // for..of 최초 실행 시, Symbol.iterator가 호출됩니다.
    [Symbol.iterator]() {
        // Symbo.iterator 메소드는 이터레이터 객체를 반환합니다.
        // 이후 for..of는 반환된 이터레이터 객체만을 대상으로 동작하는데,
        // 다음 값은 next()에서 정해집니다.
        return {
            current: this.from,
            last: this.to,

            // for..of 반복문에 의해 각 이터레이션마다 next()가 호출됩니다.
            next() {
                // next()는 객체 형태의 값 { done: ..., value: ... }를 반환합니다.
                if (this.current <= this.last) {
                    return { done: false, value: this.current++ };
                } else {
                    return { done: true };
                }
            },
        };
    },
};

for (let value of range) {
    console.log(value); // 1, 2, 3, 4, 5
}
```

이터러블 객체를 비동기적으로 만들려면 아래 작업이 필요합니다.

1. `Symbol.iterator` 대신 `Symbol.asyncIterator`를 사용해야 합니다.
2. `next()`는 promise를 반환해야 합니다.
3. 비동기 이터러블 객체를 대상으로 하는 반복 작업은 `for await (let item of iterable)` 반복문을 사용해 처리해야 합니다.

```javascript
let range = {
    from: 1,
    to: 5,

    // for await..of 최초 실행 시, Symbol.asyncIterator가 호출됩니다.
    [Symbol.asyncIterator]() {
        return {
            current: this.from,
            last: this.to,

            async next() {
                // next()는 객체 형태의 값 { done: ..., value: ... }를 반환합니다.
                // 객체는 async에 의해 자동으로 promise로 감싸집니다.

                // 비동기로 무언가를 하기 위해 await를 사용할 수 있습니다.
                await new Promise((resolve) => setTimeout(resolve, 1000));

                if (this.current <= this.last) {
                    return { done: false, value: this.current++ };
                } else {
                    return { done: true };
                }
            },
        };
    },
};

(async () => {
    for await (let value of range) {
        console.log(value); // 1, 2, 3, 4, 5
    }
})();
```

위 예시에서 볼 수 있듯이, async 이터레이터는 일반 이터레이터와 구조가 유사합니다. 하지만 아래와 같은 차이가 있습니다.

1. 객체를 비동기적으로 반복 가능하도록 하려면, `Symbol.asyncIterator` 메소드가 반드시 구현되어 있어야 합니다.
2. `Symbol.asyncIterator`는 promise를 반환하는 메소드인 `next()`가 구현된 객체를 반환해야 합니다.
3. `next()`는 `async` 메소드일 필요는 없습니다. Promise를 반환하는 메소라면 일반 메소드라도 괜찮습니다. 다만, `async`를 사용하면 `await`도 사용할 수 있기 때문에, 여기서는 편의상 `async` 메소드를 사용해 일 초의 딜레이가 생기도록 했습니다.
4. 반복 작업을 하려면 "for" 뒤에 "await"를 붙인 `for await (let value of range)`를 사용하면 됩니다. `for await (let value of range)`가 실행될 때 `range[Symbol.asyncIterator]()`가 일회 호출되는데, 그 이후에는 각 값을 대상으로 `next()`가 호출됩니다.

### ⚠️ 전개 문법 `...`은 비동기적으로 동작하지 않습니다.

일반적인 동기 이터레이터가 필요한 기능은 비동기 이터레이터와 함께 사용할 수 없습니다.

전개 문법은 일반 이터레이터가 필요로 하므로 비동기 이터레이터와 함께 사용하면 동작하지 않습니다. 전개 문법은 `await`가 없는 `for..of`와 마찬가지로 `Symbol.asyncIterator`가 아닌 `Symbol.iterator`를 찾기 때문에 에러가 발생하는 것은 당연합니다.

<br>

## async 제너레이터

일반 제너레이터에서는 `await`를 사용할 수 없습니다. 그리고 모든 값은 동기적으로 생산됩니다. `for..of` 어디에서도 딜레이를 줄 수 없습니다. 일반 제너레이터는 동기 문법입니다.

그런데 제너레이터 본문에서 `await`를 사용해야만 하는 상황이 발생하면 어떻게 해야 할까요? 아래 예시와 같이 `async`를 제너레이터 함수 앞에 붙여주면 됩니다.

```javascript
async function* generateSequence(start, end) {
    for (let i = start; i <= end; i++) {
        await new Promise((resolve) => setTimeout(resolve, 1000));

        yield i;
    }
}

(async () => {
    let generator = generateSequence(1, 5);
    for await (let value of generator) {
        console.log(value); // 1, 2, 3, 4, 5
    }
})();
```

이제 `for await..of`로 반복 가능한 async 제너레이터를 사용할 수 있습니다.

async 제너레이터를 만드는 것은 실제로도 상당히 간단합니다. `async` 키워드를 붙이기만 하면 제너레이터 안에서 promise와 기타 async 함수를 기반으로 동작하는 `await`를 사용할 수 있습니다.

async 제너레이터의 `generator.next()` 메소드는 비동기적이 되고, promise를 반환한다는 점은 일반 제너레이터와 async 제너레이터의 또 다른 차이입니다.

일반 제너레이터에서는 `result = generator.next()`를 사용해 값을 얻습니다. 반면 async 제너레이터에서는 아래와 같이 `await`를 붙여줘야 합니다.

```javascript
result = await generator.next(); // result = { value: ..., done: true/false }
```

<br>

## async 이터러블

반복 가능한 객체를 만들려면 객체에 `Symbol.iterator`를 추가해야 합니다.

```javascript
let range = {
    from: 1,
    to: 5,
    [Symbol.iterator]() {
        return <range를 반복 가능하게 만드는 next가 구현된 객체>;
    },
};
```

그런데 `Symbol.iterator`는 위 예시와 같이 `next()`가 구현된 일반 객체를 반환하는 것보다 제너레이터를 반환하도록 구현하는 경우가 더 많습니다.

```javascript
let range = {
    from: 1,
    to: 5,

    *[Symbol.iterator]() {
        for (let value = this.from; value <= this.to; value++) {
            yield value;
        }
    },
};

for (let value of range) {
    console.log(value); // 1, 2, 3, 4, 5
}
```

위 예시에서 커스텀 객체 `range`는 반복 가능하고, 제너레이터 `*[Symbol.iterator]`에는 값을 나열해주는 로직이 구현되어 있습니다.

지금 상태에서 제너레이터에 비동기 동작을 추가하려면 `Symbol.iterator`를 `Symbol.asyncIterator`로 바꿔야 합니다.

```javascript
let range = {
    from: 1,
    to: 5,

    async *[Symbol.asyncIterator]() {
        for (let value = this.from; value <= this.to; value++) {
            await new Promise((resolve) => setTimeout(resolve, 1000));

            yield value;
        }
    },
};

(async () => {
    for await (let value of range) {
        console.log(value); // 1, 2, 3, 4, 5
    }
})();
```

<br>

## Reference

https://ko.javascript.info/async-iterators-generators
