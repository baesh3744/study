# `call()`/`apply()`와 데코레이터, 포워딩

## 코드 변경 없이 캐싱 기능 추가하기

CPU를 많이 잡아먹지만 결과는 안정적인 함수 `slow(x)`가 있다고 가정해 봅시다. 결과가 안정적이라는 말은 `x`가 같으면 호출 결과도 같다는 것을 의미합니다.

`slow(x)`가 자주 호출된다면, 결과를 어딘가에 저장(캐싱)해 재연산에 걸리는 시간을 줄이고 싶을 겁니다.

아래 예시에서는 `slow()` 안에 캐싱 관련 코드를 추가하는 대신, 래퍼 함수를 만들어 캐싱 기능을 추가하였습니다.

```javascript
function slow(x) {
    console.log(`slow(${x})를 호출함`);
    return x;
}

function cachingDecorator(func) {
    let cache = new Map();

    return function (x) {
        if (cache.has(x)) {
            return cache.get(x);
        }

        let result = func(x);

        cache.set(x, result);
        return result;
    };
}

slow = cachingDecorator(slow);

console.log(slow(1));
```

`cachingDecorator()`와 같이 인수로 받은 함수의 행동을 변경시켜주는 함수를 데코레이터(decorator)라고 부릅니다.

모든 함수를 대상으로 `cachingDecorator()`를 호출 할 수 있는데, 이때 반환되는 것은 캐싱 래퍼입니다. 함수에 `cachingDecorator()`를 적용하기만 하면 캐싱이 가능한 함수를 원하는 만큼 구현할 수 있기 때문에 데코레이터 함수는 아주 유용하게 사용됩니다. 캐싱 관련 코드를 함수 코드와 분리할 수 있기 때문에 함수의 코드가 간결해진다는 장점도 있습니다.

`slow()` 함수의 본문을 수정하는 것보다 독립된 래퍼 함수 `cachingDecorator()`를 사용할 때 생기는 이점은 다음과 같습니다.

-   `cachingDecorator()`를 재사용할 수 있습니다. 원하는 함수 어디에든 `cachingDecorator()`를 적용할 수 있습니다.
-   캐싱 로직이 분리되어 `slow()` 자체의 복잡성이 증가하지 않습니다.
-   필요하다면 여러 개의 데코레이터를 조합해서 사용할 수도 있습니다.

<br>

## `func.call()`을 사용해 컨텍스트 지정하기

위에서 구현한 데코레이터는 객체 메소드레 사용하기에는 적합하지 않습니다.

```javascript
let worker = {
    someMethod() {
        return 1;
    },
    slow(x) {
        console.log(`slow(${x})를 호출함`);
        return x * this.someMethod();
    },
};

console.log(worker.slow(1)); // 기존 메소드는 잘 동작합니다.

worker.slow = cachingDecorator(worker.slow);

console.log(worker.slow(2)); // Error: Cannot read property 'someMethod' of undefined
```

래퍼 함수 `cachingDecorator()`가 기존 함수 `func(x)`를 호출하면 `this`가 `undefined`가 되기 때문에, `this.someMethod` 접근에 실패하고 에러가 발생합니다.

이를 해결하기 위해서는 `func.call()` 메소드를 통해 `this`를 명시적으로 고정시켜주어야 합니다.

```javascript
function cachingDecorator(func) {
    // ...

    return function (x) {
        // ...

        let result = func.call(this, x);

        // ...
    };
}
```

위 코드에서 `this`는 아래 과정을 거쳐 전달됩니다.

1. 데코레이터를 적용한 후에 `worker.slow()`는 래퍼 함수 `function (x) { ... }`가 됩니다.
2. `worker.slow(2)`를 실행하면, 래퍼는 `2`를 인자로 받고, `this`는 `worker`가 됩니다.
3. 결과가 캐시되지 않은 상황이라면 `func.call(this, x)`에서 현재 `this (=worker)`와 `2`를 원본 메소드에 전달합니다.

<br>

## 'Function.prototype.call()`

`Function.prototype.call()` 메소드는 `this`를 명시적으로 고정해 함수를 호출합니다.

```javascript
func.call(context, arg1, arg2, ...);
```

메소드를 호출하면 메소드의 첫 번째 인자 `context`가 `this`, 이어지는 인자 `arg1, arg2, ...`가 `func`의 인자가 된 후, `func`이 호출됩니다.

<br>

## `Function.prototype.apply()`

```javascript
func.apply(context, args);
```

`func.apply()` 메소드는 `func.call()` 메소드와 동일하게 동작하지만, `func` 함수의 인자를 받을 때 `call()` 메소드는 복수 인자를 따로따로 받는 반면, `apply()` 메소드는 복수 인자를 유사 배열 객체로 받습니다.

<br>

## 콜 포워딩(Call Forwarding)

컨텍스트와 함께 인수 전체를 다른 함수에 전달하는 것을 콜 포워딩이라고 합니다.

가장 간단한 형태의 콜 포워딩은 아래와 같습니다.

```javascript
let wrapper = function () {
    return func.apply(this, arguments);
};
```

<br>

## Reference

https://ko.javascript.info/call-apply-decorators
