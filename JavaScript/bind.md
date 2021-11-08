# 함수 바인딩

## 사라지는 `this`

객체 메소드가 객체 내부가 아닌 다른 곳에 전달되어 호출되면 `this`가 사라집니다.

```javascript
let user = {
    firstName: "John",
    sayHi() {
        console.log(`Hello, ${this.firstName}`);
    },
};

setTimeout(user.sayHi, 1000); // Hello, undefined
```

`this.firstName`이 "John"이 아닌 "undefined"가 된 이유는 `setTimeout()`에 객체에서 분리된 함수인 `user.sayHi()`가 전달되기 때문입니다. 위 예시의 마지막 줄은 아래 코드와 같습니다.

```javascript
let f = user.sayHi;
setTimeout(f, 1000); // user context를 잃어버림
```

브라우저 환경에서 `setTimeout()` 메소드는 조금 특별한 방식으로 동작합니다. 인수로 전달받은 함수를 호출할 때, `this`에 `window`를 할당합니다. 따라서 위 예시에서 `this.firstName`은 `window.firstName`이 되는데, `window` 객체에는 `firstName`이 없으므로 `undefined`가 출력됩니다. 다른 유사한 사례에서도 대부분 `this`는 `undefined`가 됩니다.

객체 메소드를 실제 메소드가 호출되는 곳 (예시에서는 `setTimeout()`) 으로 전달하는 것은 아주 흔합니다. 이렇게 메소드를 전달할 때, context도 제대로 유지하려면 어떻게 해야 할까요?

## 방법 1: 래퍼 함수

가장 간단한 해결책은 래퍼 함수를 사용하는 것입니다.

```javascript
setTimeout(function () {
    user.sayHi();
}, 1000); // Hello, John

// 또는
setTimeout(() => user.sayHi(), 1000);
```

위 예시가 의도한 대로 동작하는 이유는 외부 lexical 환경에서 `user`를 받아서 보통 때처럼 메소드를 호출했기 때문입니다.

하지만 이렇게 코드를 작성하면 약간의 취약성이 생깁니다. `setTimeout()`이 트리거 되기 전 (1초가 지나기 전) 에 `user`가 변경되면, 변경된 객체의 메소드를 호출하게 됩니다.

```javascript
setTimeout(() => user.sayHi(), 1000); // 또 다른 사용자!

// 1초가 지나기 전에 user의 값이 바뀜
user = {
    sayHi() {
        console.log("또 다른 사용자!");
    },
};
```

아래 "방법 2"를 사용하면 이런 일이 발생하지 않습니다.

<br>

## 방법 2: `Function.prototype.bind()`

모든 함수는 `this`를 수정하게 해주는 내장 메소드 `bind()`를 제공합니다.

```javascript
let boundFunc = func.bind(context);
```

`func.bind(context)`는 함수처럼 호출 가능한 "특수 객체(exotic object)"를 반환합니다. 이 객체를 호출하면 `this`가 `context`로 고정된 함수 `func()`가 반환됩니다. 따라서 `boundFunc()`를 호출하면 `this`가 고정된 `func()`를 호출하는 것과 동일한 효과를 봅니다.

아래 `funcUser()`에는 `this`가 `user`로 고정된 `func()`이 할당됩니다.

```javascript
let user = {
    firstName: "John",
};

function func() {
    console.log(this.firstName);
}

let funcUser = func.bind(user);
funcUser(); // John
```

여기서 `func.bind(user)`는 `func()`의 `this`를 `user`로 "바인딩한 변형"이라고 생각하시면 됩니다.

인수는 원본 함수 `func()`에 그대로 전달됩니다.

```javascript
let user = {
    firstName: "John",
};

function func(phrase) {
    console.log(phrase + ", " + this.firstName);
}

let funcUser = func.bind(user);
funcUser("Hello"); // Hello, John
```

<br>

### 부분 적용(Partial Application)

`this`뿐만 아니라 인수도 바인딩이 가능합니다.

`bind()`의 전체 문법은 다음과 같습니다.

```javascript
let bound = func.bind(context[, arg1[, arg2[, ...]]]);
```

`bind()`는 context를 `this`로 고정하는 것 뿐만 아니라 함수의 인자도 고정해줍니다.

```javascript
function mul(a, b) {
    return a * b;
}

let double = mul.bind(null, 2);

console.log(double(3)); // = mul(2, 3) = 6
```

`mul.bind(null, 2)`를 호출하면 새로운 함수 `double()`이 만들어집니다. `double()`에는 context가 `null`, 첫 번째 인수가 `2`인 `mul()`의 호출 결과가 전달됩니다. 추가 인수는 "그대로" 전달됩니다.

이런 방식을 부분 적용이라고 부릅니다. 부분 적용을 사용하면 기존 함수의 매개변수를 고정하여 새로운 함수를 만들 수 있습니다.

그런데 부분 함수는 왜 만드는 걸까요?

가독성이 좋은 이름 (`double()`, `triple()`) 을 가진 독립 함수를 만들 수 있다는 이점 때문입니다. 게다가 `bind()`를 사용해 첫 번째 인수를 고정할 수 있기 때문에 매번 인수를 전달할 필요도 없어집니다.

또한 부분 적용은 매우 포괄적인 함수를 기반으로 덜 포괄적인 변형 함수를 만들 수 있다는 점에서 유용합니다. 함수 `send(from, to, text)`가 있을 때, 객체 `user` 안에서 부분 적용을 활용하면, 전송 주체가 현재 사용자인 함수 `sendTo(to, text)`를 구현할 수 있습니다.

<br>

### Context 없는 부분 적용

인수 일부는 고정하고 context `this`는 고정하고 싶지 않다면 어떻게 해야 할까요?

`bind()`만으로는 context를 생략하고 인수로 바로 뛰어넘지 못합니다. 그러나 인수만 바인딩해주는 헬퍼 함수 `partial()`을 구현하는 건 쉽습니다.

```javascript
function partial(func, ...argsBound) {
    return function (...args) {
        return func.call(this, ...argsBound, ...args);
    };
}

let user = {
    firstName: "John",
    say(time, phrase) {
        console.log(`[${time}] ${this.firstName}: ${phrase}`);
    },
};

user.sayNow = partial(
    user.say,
    new Date().getHours() + ":" + new Date().getMinutes()
);

user.sayNow("Hello"); // [10:00] John: Hello
```

`partial(func[, arg1[, arg2[, ...]]])`을 호출하면 래퍼가 반환됩니다. 래퍼를 호출하면 `func()`이 다음과 같은 방식으로 동작합니다.

-   동일한 `this`를 받습니다. (`user.sayNow()`는 `user`를 대상으로 호출됩니다.)
-   `partial()`을 호출할 때 받은 인수(`"10:00"`)는 `...argsBound`에 전달됩니다.
-   래퍼에 전달된 인수(`"Hello"`)는 `...args`가 됩니다.

lodash 라이브러리의 `_.partial()`을 사용하면 context 없는 부분 적용을 직접 구현하지 않아도 됩니다.

<br>

## Reference

https://ko.javascript.info/bind
