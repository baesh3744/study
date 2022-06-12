# Symbol

## Symbol이란

Symbol은 유일한 식별자(unique identifier)를 만들고 싶을 때 사용합니다.

```javascript
// Symbol id에는 "id"라는 설명이 붙습니다.
let id = Symbol("id");
```

Symbol은 유일성이 보장되는 자료형이기 때문에, 설명이 동일한 Symbol을 여러 개 만들어도 각 Symbol 값은 다릅니다. Symbol에 붙이는 설명은 어떤 것에도 영향을 주지 않는 이름표 역할만 합니다.

```javascript
let id1 = Symbol("id");
let id2 = Symbol("id");

console.log(id1 == id2); // false
```

### Symbol은 문자형으로 자동 형 변환되지 않습니다.

JavaScript에서는 문자형으로의 암시적 형 변환이 비교적 자유롭게 일어나는 편입니다. `alert` 함수가 거의 모든 값을 인자로 받을 수 있는 이유도 이 때문입니다. 그러나 Symbol은 예외입니다. Symbol 값은 다른 자료형으로 암시적 형 변환이 이루어지지 않습니다.

```javascript
let id = Symbol("id");
alert(id); // TypeError: Cannot convert a Symbol value to a string
```

JavaScript에서는 "언어 차원의 보호장치(language guard)"를 마련해 Symbol이 다른 형으로 변환되지 않게 막아줍니다.

Symbol을 반드시 출력해야 하는 상화이라면 아래와 같이 `.toString()` 메소드를 명시적으로 호출해주면 됩니다.

```javascript
let id = Symbol("id");
alert(id.toString()); // Symbol(id)
```

`symbol.description` 프로퍼티를 이용하면 설명만 보여주는 것도 가능합니다.

```javascript
let id = Symbol("id");
alert(id.description); // id
```

<br>

## "히든" 프로퍼티(Hidden Property)

Symbol을 이용하면 히든 프로퍼티를 만들 수 있습니다. 히든 프로퍼티는 외부 코드에서 접근이 불가능하고 값도 덮어쓸 수 없는 프로퍼티입니다.

서드파티 코드에서 가지고 온 `user`라는 객체가 여러 개 있고, `user`를 이용해 어떤 작업을 해야 하는 상황이라고 가정해 봅시다.

`user`에 식별자를 붙여주도록 합시다. 식별자는 Symbol을 이용해 만들도록 하겠습니다.

```javascript
let user = {
    name: "John",
};

let id = Symbol("id");

user[id] = 1;

console.log(user[id]);
```

그런데 문자열 `"id"`를 키로 사용해도 되는데 `Symbol("id")`를 사용한 이유가 무엇일까요?

`user`는 서드파티 코드에서 가지고 온 객체이므로 함부로 새로운 프로퍼티를 추가할 수 없습니다. 그런데 Symbol은 서드파티 코드에서 접근할 수 없기 때문에, Symbol을 사용하면 서드파티 코드가 모르게 `user`에 식별자를 부여할 수 있습니다.

제3의 스크립트에서 `user`를 식별해야 하는 상황이 벌어졌다고 가정해 봅시다. `user`의 원천인 서드파티 코드, 현재 작성 중인 스크립트, 제3의 스크립트가 각자 서로의 코드도 모른 채 `user`를 식별해야 하는 상황이 벌어졌습니다.

제3의 스크립트에서는 아래와 같이 `Symbol("id")`를 이용해 전용 식별자를 만들어 사용할 수 있습니다.

```javascript
let id = Symbol("id");
user[id] = "제3 스크립트의 id 값";
```

Symbol은 유일성이 보장되므로 우리가 만든 식별자와 제3의 스크립트에서 만든 식별자가 이름이 같더라도 충동하지 않습니다.

만약 Symbol 대신 문자열 `"id"`를 사용해 식별자를 만들었다면 충돌이 발생할 가능성이 있습니다.

```javascript
let user = { name: "John" };

// 문자열 "id"를 사용해 식별자를 만들었습니다.
user.id = "현재 스크립트의 id 값";

// 만약 제3의 스크립트가 현재 스크립트와 동일하게 문자열 "id"를 이용해 식별자를 만들었다면
// 의도치 않게 값이 덮어 쓰여져 현재 스크립트에서 만든 식별자는 무의미해집니다.
user.id = "제3 스크립트의 id 값";
```

### Symbols in a literal

객체 리터럴 `{...}`을 사용해 객체를 만든 경우, 대괄호를 사용해 Symbol 키를 만들어야 합니다.

```javascript
let id = Symbol("id");

let user = {
    name: "John",
    [id]: 123,
};
```

### Symbol은 `for..in`에서 배제됩니다.

키가 Symbol인 프로퍼티는 `for..in` 반복문에서 배제됩니다.

```javascript
let id = Symbol("id");
let user = {
    name: "John",
    age: 30,
    [id]: 123,
};

for (let key in user) console.log(key); // name, age
```

`Objects.keys(user)`에서도 키가 Symbol인 프로퍼티는 배제됩니다. "Symbol형 프로퍼티 숨기기(hiding symbolic property)"라 불리는 이런 원칙 덕분에 외부 스크립트나 라이브러리는 Symbol형 키를 가진 프로퍼티에 접근하지 못합니다.

그런데 `Object.assign()`은 키가 Symbol인 프로퍼티를 배제하지 않고 객체 내 모든 프로퍼티를 복사합니다.

```javascript
let id = Symbol("id");
let user = {
    [id]: 123,
};

let clone = Object.assign({}, user);
console.log(clone[id]); // 123
```

<br>

## 전역 Symbol

Symbol은 이름이 같더라도 모두 별개로 취급됩니다. 그런데 이름이 같은 Symbol이 같은 개체를 가리키길 원하는 경우도 가끔 있습니다. 애플리케이션 곳곳에서 Symbol `"id"`를 이용해 특정 프로퍼티에 접근해야 한다고 가정해 봅시다.

전역 Symbol 레지스트리(global symbol registry)는 이런 경우를 위해 만들어졌습니다. 전역 Symbol 레지스트리 안에 Symbol을 만들고 해당 Symbol에 접근하면, 이름이 같은 경우 항상 동일한 Symbol을 반환해줍니다.

레지스트리 안에 있는 Symbol을 읽거나 새로운 Symbol을 생성하려면 `Symbol.for(key)`를 사용하면 됩니다. 이 메소드를 호출하면 이름이 `key`인 Symbol을 반환합니다. 조건에 맞는 Symbol이 레지스트리 안에 없으면 새로운 Symbol `Symbol(key)`를 만들고 레지스트리 안에 저장합니다.

```javascript
// 전역 레지스트리에서 Symbol을 읽습니다.
let id = Symbol.for("id");

// 동일한 이름을 이용해 Symbol을 다시 읽습니다.
let idAgain = Symbol.for("id");

// 두 Symbol은 같습니다.
console.log(id === idAgain); // true
```

전역 Symbol 레지스트리 안에 있는 Symbol은 전역 Symbol이라고 불립니다.

### Symbol.keyFor()

전역 Symbol을 찾을 때 사용되는 `Symbol.for(key)`에 반대되는 메소드도 있습니다. `Symbol.keyFor(sym)`을 사용하면 이름을 얻을 수 있습니다.

```javascript
// 이름을 이용해 Symbol을 찾습니다.
let sym = Symbol.for("name");
let sym2 = Symbol.for("id");

// Symbol을 이용해 이름을 얻습니다.
console.log(Symbol.keyFor(sym)); // name
console.log(Symbol.keyFor(sym2)); // id
```

`Symbol.keyFor()`은 전역 Symbol 레지스트리를 뒤져서 해당 Symbol의 이름을 얻어냅니다. 검색 범위가 전역 Symbol 레지스트리이기 때문에 전역 Symbol이 아닌 Symbol에는 사용할 수 없습니다. 전역 Symbol이 아닌 Symbol이 인자로 넘어오면 `Symbol.keyFor()`는 `undefined`를 반환합니다.

전역 Symbol이 아닌 모든 Symbol은 `description` 프로퍼티가 있습니다. 일반 Symbol에서 이름을 얻고 싶으면 `description` 프로퍼티를 사용하면 됩니다.

```javascript
let globalSymbol = Symbol.for("name");
let localSymbol = Symbol("name");

console.log(Symbol.keyFor(globalSymbol)); // name
console.log(Symbol.keyFor(localSymbol)); // undefined
console.log(localSymbol.description); // name
```

<br>

## 시스템 Symbol

"시스템 Symbol"은 JavaScript 내부에서 사용되는 Symbol입니다. 시스템 Symbol을 활용하면 객체를 미세 조정할 수 있습니다.

-   `Symbol.hasInstance()`
-   `Symbol.isConcatSpreadable()`
-   `Symbol.iterator()`
-   `Symbol.toPrimitive()`
-   기타 등등..

<br>

## Reference

https://ko.javascript.info/symbol
