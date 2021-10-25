# this

실행되는 context (global, function 또는 eval) 의 프로퍼티는 non-strict mode에서는 항상 객체를 참조하며, strict mode에서는 어떠한 값이든 될 수 있습니다.

<br>

## Global Context

Global context에서 `this`는 strict mode 여부에 관계 없이 전역 객체를 참조합니다.

```javascript
// 웹 브라우저에서는 window 객체가 전역 객체
console.loge(this === window); // true

this.b = "MDN";
console.log(window.b); // "MDN"
console.log(b); // "MDN"
```

<br>

## Function Context

함수 내부에서 `this` 값은 함수를 호출한 방법에 의해 좌우됩니다.

### 단순 호출

Strict mode가 아니고 `this`의 값이 호출에 의해 설정되지 않으면, 기본값으로 브라우저에서는 전역 객체인 `window`를 참조합니다.

```javascript
function f1() {
    return this;
}

// 브라우저
f1() === window; // true

// Node.js
f1() === global; // true
```

Strict mode에서 `this` 값은 실행 context에 진입하며 설정되는 값을 유지하므로 `undefined`로 남아있습니다.

```javascript
function f2() {
    "use strict";
    return this;
}

f2() === undefined; // true
```

`this`의 값을 한 context에서 다른 context로 넘기려면 `call()`이나 `apply()`를 사용합니다.

```javascript
var obj = { a: "Custom" };
var a = "Global";

function whatsThis() {
    return this.a;
}

whatsThis(); // this는 'Global'. 함수 내에서 설정되지 않았으므로 global/window 객체로 초기값을 설정합니다.
whatsThis.call(obj); // this는 'Custom'. 함수 내에서 obj로 설정합니다.
whatsThis.apply(obj); // this는 'Custom'. 함수 내에서 obj로 설정합니다.
```

Non-strict mode에서 `this`로 전달된 값이 객체가 아닌 경우, `call`과 `apply`는 이를 객체로 변환하기 위한 시도를 합니다. `null`과 `undefined` 값은 전역 객체가 됩니다. `7`이나 `'foo'`와 같은 원시값은 관련된 생성자를 사용해 객체로 변환되며, 따라서 `7`은 `new Number(7)`, `'foo'`는 `new String('foo')`에 의해 객체로 변환됩니다.

```javascript
function bar() {
    console.log(Object.prototype.toString.call(this));
}

bar.call(7); // [object Number]
bar.call("foo"); // [object String]
bar.call(undefined); // [object global]
```

<br>

### `bind` 메소드

ECMAScript 5는 `Function.prototype.bind`를 도입했습니다. `f.bind(someObject)`를 호출하면 `f`와 같은 코드와 범위를 가졌지만 `this`는 원본 함수를 가진 새로운 함수를 생성합니다. 새 함수의 `this`는 호출 방식과 상관없이 영구적으로 `bind()`의 첫 번째 매개변수로 고정됩니다.

```javascript
function f() {
    return this.a;
}

var g = f.bind({ a: "azerty" });
console.log(g()); // azerty

var h = g.bind({ a: "yoo" }); // bind는 한 번만 동작합니다!
console.log(h()); // azerty

var o = { a: 37, f: f, g: g, h: h };
console.log(o.a, o.f(), o.g(), o.h()); // 37, 37, azerty, azerty
```

<br>

### 화살표 함수

화살표 함수에서 `this`는 자신을 감싼 정적 범위입니다. 전역 코드에서는 전역 객체를 가리킵니다.

```javascript
var globalObject = this;
var foo = () => this;
console.log(foo() === globalObject); // true
```

화살표 함수를 `call()`, `bind()`, `apply()`를 사용해 호출할 때 `this`의 값을 정해주더라도 무시합니다. 사용할 매개변수를 정해주는 건 문제 없지만, 첫 번째 매개변수(`thisArg`)는 `null`을 지정해야 합니다.

```javascript
// 객체로서 메소드 호출
var obj = { func: foo };
console.log(obj.func() === globalObject); // true

// call을 사용한 this 설정 시도
console.log(foo.call(obj) === globalObject); // true

// bind를 사용한 this 설정 시도
foo = foo.bind(obj);
console.log(foo() === globalObject); // true
```

어떤 방법을 사용하든 `foo`의 `this`는 생성 시점의 것으로 설정됩니다. 다른 함수 내에서 생성된 화살표 함수에도 동일하게 적용됩니다. `this`는 둘러싸여진 lexical context의 것으로 유지됩니다.

```javascript
// this를 반환하는 메소드 bar를 갖는 obj를 생성합니다.
// 반환된 함수는 화살표 함수로 생성되었으므로,
// this는 감싸진 함수의 this로 영구적으로 묶입니다.
// bar의 값은 호출에서 설정될 수 있으며, 이는 반환된 함수의 값을 설정하는 것입니다.
var obj = {
    bar: function () {
        var x = () => this;
        return x;
    },
};

// obj의 메소드로써 bar를 호출하고, this를 obj로 설정
// 반환된 함수로의 참조를 fn에 할당
var fn = obj.bar();

// this 설정 없이 fn을 호출하면,
// 기본값으로 global 객체 또는 strict mode에서는 undefined
console.log(fn() === obj); // true

// 화살표 함수의 this를 bar 메소드 내부에서 호출하면
// fn2의 this를 따르므로 window를 반환할 것입니다.
var fn2 = obj.bar;
console.log(fn2()() === window); // true
```

위 예시에서 `obj.bar`에 할당된 함수 (익명 함수 A라고 지칭) 는 화살표 함수로 생성된 다른 함수 (익명 함수 B라고 지칭) 를 반환합니다. 결과로써 함수 B가 호출될 때 B의 `this`는 영구적으로 `obj.bar`(함수 A)의 `this`로 설정됩니다. 반환된 함수(함수 B)가 호출될 때, `this`는 항상 초기에 설정된 값입니다. 위 예시에서 함수 B의 `this`는 함수 A의 `this`인 `obj`로 설정되므로, 일반적으로 `this`를 `undefined`나 global 객체로 설정하는 방식으로 호출할 때도 obj의 설정은 유지됩니다.

<br>

### 객체의 메소드로서

함수를 어떤 객체의 메소드로 호출하면 `this`의 값은 그 객체를 사용합니다.

다음 예제에서 `o.f()`를 실행할 때 `o` 객체가 함수 내부의 `this`와 연결됩니다.

```javascript
var o = {
    prop: 37,
    f: function () {
        return this.prop;
    },
};

console.log(o.f()); // 37
```

이 동작은 함수가 정의된 방법이나 위치에 전혀 영향을 받지 않습니다. 위 예제에서 `o`의 정의 중 `f` 함수를 구성원으로 내부에 정의하였습니다. 그러나 함수를 먼저 정의하고 나중에 `o.f`를 추가하여도 동일한 결과를 보입니다.

```javascript
var o = { prop: 37 };

function independent() {
    return this.prop;
}

o.f = independent;

console.log(o.f()); // 37
```

마찬가지로, `this`는 가장 가까운 멤버를 참조합니다. 다음 예제에서 함수를 실행할 때, 객체 `o.b`의 메소드 `g`로서 호출합니다. 함수가 실행되는 동안, 함수 내부의 `this`는 `o.b`를 나타냅니다.

```javascript
o.b = { g: independent, prop: 42 };
console.log(o.b.g()); // 42
```

#### 객체의 프로토타입 체인에서의 `this`

메소드가 어떤 객체의 프로토타입 체인 위에 존재하면, `this`의 값은 그 객체가 메소드를 가진 것 마냥 설정됩니다.

```javascript
var o = {
    f: function () {
        return this.a + this.b;
    },
};
var p = Object.create(o);
p.a = 1;
p.b = 4;

console.log(p.f()); // 5
```

위 예제에서 변수 `p`에 할당된 객체는 `f` 속성을 가지고 있지 않고 프로토타입으로부터 상속받습니다. `p.f()`를 호출하면, `f`는 `p`의 메소드로써 호출되므로, `this`는 `p` 객체를 참조합니다.

#### 접근자와 설정자의 `this`

접근자나 설정자로 사용하는 함수의 `this`는 접근하거나 설정하는 속성을 가진 객체로 묶입니다.

```javascript
function sum() {
    return this.a + this.b + this.c;
}

var o = {
    a: 1,
    b: 2,
    c: 3,
    get average() {
        return (this.a + this.b + this.c) / 3;
    },
};

Object.defineProperty(o, "sum", {
    get: sum,
    enumerable: true,
    configurable: true,
});

console.log(o.average, o.sum); // 2, 6
```

<br>

### 생성자로서

함수를 `new` 키워드와 함께 생성자로 사용하면 `this`는 새로 생긴 객체에 묶입니다.

```javascript
function C() {
    this.a = 37;
}

var o = new C();
console.log(o.a); // 37

function C2() {
    this.a = 37;
    return { a: 38 };
}

o = new C2();
console.log(o.a); // 38
```

<br>

### DOM Event Handler로서

함수를 event handler로 사용하면 `this`는 event listener가 설정된 요소를 참조합니다. (일부 브라우저는 `addEventListener()` 외의 다른 방법으로 추가한 handler에 대해서는 이 규칙을 따르지 않습니다.)

```javascript
function bluify(e) {
    console.log(this === e.currentTarget); // 언제나 true
    console.log(this === e.target); // currentTarget과 target이 같은 객체면 true
}

someElement.addEventListener("click", bluify, false);
```

<br>

### Inline Event Handler에서

코드를 inline event handler로 사용하면 `this`는 handler를 배치한 DOM 요소로 설정됩니다.

```html
<button onclick="alert(this.tagName.toLowerCase());">this 표시</button>
```

위 경고창은 `button`을 보여줍니다. 단, 가장 바깥쪽 코드만 `this`를 이런 방식으로 설정합니다.

```html
<button onclick="alert((function() { return this; })());">
    내부 this 표시
</button>
```

위의 경우, 내부 함수의 `this`는 정해지지 않았으므로 `global`/`window` 객체를 반환합니다. 즉, non-strict mode에서 `this`를 설정하지 않은 경우의 기본값입니다.

<br>

## Reference

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this
