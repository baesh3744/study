# 제너레이터(Generator)

일반 함수는 하나의 값 (혹은 0개의 값) 을 반환합니다. 하지만 제너레이터(generator)를 사용하면 여러 개의 값을 필요에 따라 하나씩 반환(yield)할 수 있습니다. 제너레이터와 이터러블 객체를 함께 사용하면 손쉽게 데이터 스트림을 만들 수 있습니다.

<br>

## 제너레이터 함수

제너레이터를 만들려면 "제너레이터 함수"라 불리는 특별한 문법 구조 `function*`이 필요합니다.

```javascript
function* generateSequence() {
    yield 1;
    yield 2;
    return 3;
}
```

제너레이터 함수는 일반 함수와 동작 방식이 다릅니다. 제너레이터 함수를 호출하면 코드가 실행되지 않고, 대신 실행을 처리하는 특별 객체 "제너레이터 객체"가 반환됩니다.

```javascript
let generator = generateSequence();
console.log(generator); // [object Generator]
```

`next()`는 제너레이터의 주요 메소드입니다. `next()`를 호출하면 가장 가까운 `yield <value>`문을 만날 때까지 실행이 지속됩니다. `value`를 생략할 수도 있는데, 이 경우에는 `undefined`가 됩니다. 이후, `yield <value>`문을 만나면 실행이 멈추고 산출하고자 하는 값인 `value`가 바깥 코드에 반환됩니다.

`next()`는 항상 아래 두 property를 가진 객체를 반환합니다.

-   `value`: 산출 값
-   `done`: 함수 코드 실행이 끝났으면 `true`, 아니라면 `false`

```javascript
let one = generator.next();
console.log(JSON.stringify(one)); // { value: 1, done: false }

let two = generator.next();
console.log(JSON.stringify(two)); // { value: 2, done: false }

let three = generator.next();
console.log(JSON.stringify(three)); // { value: 3, done: true }. 제너레이터 종료
```

제너레이터가 종료된 상태에서는 `generator.next()`를 호출해도 객체 `{ done: true }`만 반환됩니다.

<br>

## 제너레이터와 이터러블

제너레이터는 이터러블이므로 `for..of` 반복문을 사용해 값을 얻을 수 있습니다.

```javascript
for (let value of generator) {
    console.log(value); // 1, 2
}
```

위 예시를 실행하면 `1`과 `2`만 출력되고 `3`은 출력되지 않습니다. 이유는 `for..of` 이터레이션이 `done: true`일 때 마지막 `value`를 무시하기 때문입니다. 그러므로 `for..of`를 사용했을 때 모든 값이 출력되길 원한다면 `yield`로 값을 반환해야 합니다.

```javascript
function* generateSequence() {
    yield 1;
    yield 2;
    yield 3;
}

let generator = generateSequence();

for (let value of generator) {
    console.log(value); // 1, 2, 3
}
```

제너레이터는 이터러블 객체이므로 제너레이터에도 전개 문법(`...`) 같은 관련 기능을 사용할 수 있습니다.

```javascript
let sequence = [0, ...generateSequence()];
console.log(sequence); // 0, 1, 2, 3
```

<br>

## 제너레이터 컴포지션

제너레이터 컴포지션(generator composition)은 제너레이터 안에 제너레이터를 "임베딩(embedding, composing)"할 수 있게 해주는 제너레이터의 특별 기능입니다.

먼저, 연속된 숫자를 생성하는 제너레이터 함수를 만들어보겠습니다.

```javascript
function* generateSequence(start, end) {
    for (let i = start; i <= end; i++) yield i;
}
```

그리고 이 함수를 기반으로 좀 더 복잡한 값을 연속해서 생성하는 함수를 만들어봅시다. 값 생성 규칙은 다음과 같습니다.

-   처음엔 숫자 "0"부터 "9"까지를 생성합니다. (문자 코드 48부터 57까지)
-   이어서 알파벳 대문자 "A"부터 "Z"까지를 생성합니다. (문자 코드 65부터 90까지)
-   이어서 알파벳 소문자 "a"부터 "z"까지를 생성합니다. (문자 코드 97부터 122까지)

일반 함수로는 여러 개의 함수를 만들고 그 호출 결과를 어딘가에 저장한 후 다시 그 결과들을 조합해야 원하는 기능을 구현할 수 있습니다. 하지만 제너레이터의 특수 문법 `yield*`를 사용하면 제너레이터를 다른 제너레이터에 "끼워 넣을 수" 있습니다.

```javascript
function* generatePasswordCodes() {
    // 0..9
    yield* generateSequence(48, 57);

    // A..Z
    yield* generateSequence(65, 90);

    // a..z
    yield* generateSequence(97, 122);
}

let str = "";

for (let code of generatePasswordCodes()) {
    str += String.fromCharCode(code);
}

console.log(str); // 0..9A..Za..z
```

`yield*` 지시자는 실행을 다른 제너레이터에 위임합니다. 여기서 "위임"은 `yield* gen`이 제너레이터 `gen`을 대상으로 반복을 수행하고, 산출 값들을 바깥으로 전달한다는 것을 의미합니다. 마치 외부 제너레이터에 의해 값이 산출된 것처럼 말이죠.

중첩 제너레이터(`generateSequence()`)의 코드를 직접 써줘도 결과는 같습니다.

```javascript
function* generateAlphaNum() {
    // yield* generateSequence(48, 57);
    for (let i = 48; i <= 57; i++) yield i;

    // yield* generateSequence(65, 90);
    for (let i = 65; i <= 90; i++) yield i;

    // yield* generateSequence(97, 122);
    for (let i = 97; i <= 122; i++) yield i;
}
```

제너레이터 컴포지션을 사용하면 한 제너레이터의 흐름을 자연스럽게 다른 제너레이터에 삽입할 수 있습니다. 제너레이터 컴포지션을 사용하면 중간 결과 저장 용도의 추가 메모리가 필요하지 않습니다.

<br>

## `yield`를 사용해 제너레이터 안·밖으로 정보 교환하기

지금까지 배운 제너레이터는 값을 생성해주는 특수 문법을 가진 이터러블 객체와 유사했습니다. 그런데 사실 제너레이터는 더 강력하고 유연한 기능을 제공합니다. `yield`가 양방향 길과 같은 역할을 하기 때문입니다. `yield`는 결과를 바깥으로 전달할 뿐만 아니라 값을 제너레이터 안으로 전달하기까지 합니다. 값을 안·밖으로 전달하려면 `generator.next(arg)`를 호출해야 합니다. 이때 인수 `arg`는 `yield`의 결과가 됩니다.

```javascript
function* gen() {
    let result = yield "2 + 2 = ?"; // 질문을 제너레이터 밖 코드에 던지고 답을 기다립니다.
    console.log(result);
}

let generator = gen();

let question = generator.next().value;

generator.next(4); // 결과를 제너레이터 안으로 전달합니다.
```

1. `generator.next()`를 처음 호출할 때는 항상 인수가 없어야 합니다. 인수가 넘어오더라도 무시되어야 합니다. `generator.next()`를 호출하면 실행이 시작되고 첫 번째 `yield "2 + 2 = ?"`의 결과가 반환됩니다. 이 시점에서 제너레이터가 실행을 잠시 멈춥니다.
2. 그 후, `yield`의 결과가 제너레이터를 호출하는 외부 코드에 있는 변수 `question`에 할당됩니다.
3. 마지막 줄 `generator.next(4)`에서 제너레이터가 다시 시작되고 "4"는 `result`에 할당됩니다.

<br>

## `generator.throw()`

예시를 통해 살펴보았듯이 외부 코드는 `yield`의 결과가 될 값을 제너레이터 안에 전달하기도 합니다. 그런데 외부 코드가 에러를 만들거나 던질 수도 있습니다. 에러를 `yield` 안으로 전달하려면 `generator.throw(err)`를 호출해야 합니다. `generator.throw(err)`를 호출하게 되면 `err`는 `yield`가 있는 줄로 던져집니다.

```javascript
function* gen() {
    try {
        let result = yield "2 + 2 = ?"; // (*)
        console.log(
            "위에서 에러가 던져졌기 때문에 실행 흐름은 여기까지 다다르지 못합니다."
        );
    } catch (e) {
        console.log(e);
    }
}

let generator = gen();
let question = generator.next().value;

generator.throw(new Error("데이터베이스에서 답을 찾지 못했습니다.")); // (**)
```

`(**)`에서 제너레이터 안으로 던진 에러는 `yield`와 함께 `(*)`에서 예외를 만듭니다. 예외는 `try..catch`에서 잡히고, 관련 정보는 출력됩니다. 제너레이터 안에서 예외를 처리하지 않았다면 예외는 여타 예외와 마찬가지로 제너레이터 호출 코드(외부 코드)로 "떨어져 나옵니다".

`generator.throw()`에서 제너레이터를 호출하고 있으므로 아래와 같이 에어를 밖에서 잡아도 됩니다.

```javascript
function* gen() {
    let result = yield "2 + 2 = ?";
}

let generator = gen();
let question = generator.next().value;

try {
    generator.throw(new Error("데이터베이스에서 답을 찾지 못했습니다."));
} catch (e) {
    console.log(e);
}
```

이렇게 제너레이터에서 에러를 잡지 못하면 에러는 제너레이터 호출 코드 바깥으로 떨어져 나갑니다. 여기서도 에러를 잡지 못하면 스크립트가 죽게 됩니다.

<br>

## `generator.return()`

`generator.return(value)`는 제너레이터 실행을 끝내고 `value` 값을 반환합니다.

```javascript
function* gen() {
    yield 1;
    yield 2;
    yield 3;
}

const g = gen();

g.next(); // { value: 1, done: false }
g.return("foo"); // { value: "foo", done: true }
g.next(); // { value: undefined, done: true }
```

종료된 제너레이터에서 `generator.return()`을 다시 호출하면 `value`를 다시 반환합니다.

`generator.return()`은 특정 상황에서 제너레이터를 종료하고 싶을 때 유용합니다.

<br>

## Reference

https://ko.javascript.info/generators
