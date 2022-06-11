# 상속과 프로토타입 체인

상속 관점에서 JavaScript의 유일한 생성자는 객체뿐입니다. 각각의 객체는 `[[Prototype]]`이라는 private 속성을 가지는데, 이는 자신의 프로토타입이 되는 다른 객체를 가리킵니다. 그 객체의 프로토타입 또한 프로토타입을 가지고 잇고 이것이 반복되다, 결국 `null`을 프로토타입으로 가지는 객체에서 끝납니다. `null`은 더 이상의 프로토타입이 없다고 정의되며, 프로토타입 체인의 종점 역할을 합니다.

<br>

## 프로토타입 체인을 이용한 상속

### 속성 상속

JavaScript 객체는 자기만의 속성과 프로토타입 객체에 대한 링크를 가집니다. 객체의 어떤 속성에 접근하려할 때, 그 객체 자체 속성 뿐만 아니라 객체의 프로토타입, 그 프로토타입의 프로토타입 등 프로토타입 체인의 종단에 이를 때까지 그 속성을 탐색합니다.

객체의 속성에 값을 지정하면 "자기만의 속성"이 생깁니다. 단, getter나 setter가 적용되는 속성이 상속되는 경우에는 예외적인 규칙이 적용됩니다.

```javascript
// o라는 객체가 있고, 속성 'a'와 'b'를 갖고 있습니다.
let f = function () {
    this.a = 1;
    this.b = 2;
};
let o = new f(); // { a: 1, b: 2 }

// f 함수의 prototype 속성 값들을 추가합니다.
f.prototype.b = 3;
f.prototype.c = 4;

// f.prototype = { b: 3, c: 4 }; 라고 작성하지 마세요. 해당 코드는 prototype chain을 망가뜨립니다.
// o.[[Prototype]]은 속성 'b'와 'c'를 가지고 있습니다.
// o.[[Prototype]].[[Prototype]]은 Object.prototype입니다.
// 마지막으로 o.[[Prototype]].[[Prototype]].[[Prototype]]은 null입니다.
// null은 프로토타입의 종단을 말하며, 정의에 의해서 추가적인 [[Prototype]]은 없습니다.
// { a: 1, b: 2 } ---> { b: 3, c: 4 } ---> Object.prototype ---> null

console.log(o.a); // 1
// o는 'a'라는 속성을 가지는가? 예. 속성의 값은 1입니다.

console.log(o.b); // 2
// o는 'b'라는 속성을 가지는가? 예. 속성의 값은 2입니다.
// 프로토타입 역시 'b'라는 속성을 가지지만 이 값은 신경쓰지 않습니다. 이것을 "속성의 가려짐(property shadowing)"이라고 부릅니다.

console.log(o.c); // 4
// o는 'c'라는 속성을 가지는가? 아니요. 프로토타입을 확인합니다.
// o.[[Prototype]]은 'c'라는 속성을 가지는가? 예. 속성의 값은 4입니다.

console.log(o.d); // undefined
// o는 'd'라는 속성을 가지는가? 아니요. 프로토타입을 확인합니다.
// o.[[Prototype]]은 'd'라는 속성을 가지는가? 아니요. 다시 프로토타입을 확인합니다.
// o.[[Prototype]].[[Prototype]]은 null입니다. 찾는 것을 그만둡니다.
// 속성이 발견되지 않았기 때문에 undefined를 반환합니다.
```

### 메소드 상속

JavaScript에 "메소드"라는건 없습니다. 하지만 객체의 속성으로 함수를 지정할 수 있고 속성 값을 사용하듯 쓸 수 있습니다. 속성 값으로 지정한 함수의 상속 역시 속성의 상속과 동일합니다. (단, 위에서 언급한 "속성의 가려짐" 대신 "메소드 오버라이딩(method overriding)"이라는 용어를 사용합니다.)

상속된 함수가 실행될 때, 함수의 위치 (상속된 객체의 속성인지 프로토타입의 속성인지) 에 상관없이 `this`라는 변수는 상속된 객체를 가리킵니다.

```javascript
var o = {
    a: 2,
    m: function () {
        return this.a + 1;
    },
};

console.log(o.m()); // 3
// o.m을 호출하면 `this`는 o를 가리킵니다.

var p = Object.create(o);
// p는 o를 프로토타입으로 가지는 객체입니다.

p.a = 12;
console.log(p.m()); // 13
// p.m이 호출될 때 `this`는 'p'를 가리킵니다.
// 따라서 o의 함수 m을 상속 받으며, 'this.a'는 p.a를 나타내며 p의 개인 속성 'a'가 됩니다.
```

<br>

## 프로토타입 상속의 종류

### 위임형 상속(Delegation Inheritance)

위임형 상속에서 프로토타입 객체는 다른 객체의 기반이 됩니다. 위임 프로토타입을 상속받을 경우 새 객체는 해당 프로토타입에 대한 참조를 가지고 있습니다.

새 객체의 속성에 접근할 때, 해당 객체가 직접적으로 속성을 소유하고 있는지 먼저 체크합니다. 없다면 다음 순서로 `[[Prototype]]`을 체크합니다. 이 과정은 프로토타입 체인을 따라서 모든 객체의 프로토타입 체인의 최상위에 있는 객체인 `Object.prototype`에 도달할 때까지 반복됩니다.

메소드를 위임할 경우, 모든 객체가 각 메소드에 대해 하나의 코드를 공유하므로 메모리를 절약할 수 있습니다.

```javascript
class Greeter {
    constructor(name) {
        this.name = name || "John Doe";
    }
    hello() {
        return `Hello, my name is ${this.name}`;
    }
}

const george = new Greeter("George");
const msg = george.hello();
console.log(msg); // Hello, my name is George
```

`Object.create(null)`을 통해 프로토타입을 `null`로 지정하여 속성 위임 없이 객체를 생성할 수 있습니다.

이 방법의 큰 단점 중 하나는 상태를 저장하는데 그리 좋은 방법이 아니라는 것입니다. 객체나 배열의 상태를 변경하게 되면 같은 프로토타입을 공유하는 모든 객체의 상태가 변경됩니다. 상태 변경이 전파되는 것을 막으려면 각 객체마다 상태 값의 복사본을 만들어야 합니다.

### 연결형 상속(Concatenative inheritance)

연결형 상속은 한 객체의 속성을 다른 객체에 모두 복사함으로써 상속을 구현하는 방법입니다.

이 상속법은 JavaScript 객체의 동적 확장성을 이용한 방법입니다. 객체 복사는 속성의 초기값을 저장하기 위한 좋은 방법입니다. 이 방식은 `Object.assign()`을 통해 구현하는 것이 보통이며 ES6 이전에 Lodash, Underscore, jQuery 등의 라이브러리들이 `.extend()`와 비슷한 메소드로 제공한 방법입니다.

```javascript
const proto = {
    hello: function hello() {
        return `Hello, my name is ${this.name}`;
    },
};

const george = Object.assign({}, proto, { name: "George" });
const msg = george.hello();
console.log(msg); // Hello, my name is George
```

연결형 상속은 매우 좋은 방법이며 클로져와 같이 사용한다면 훨씬 효과적인 상속 방식입니다.

### 함수형 상속(Functional Inheritance)

이 방법은 새 속성들을 연결형 상속으로 쌓되 상속 기능을 Factory 함수로 만들어 사용하는 방식입니다.

기존의 객체를 확장하는데 쓰이는 함수를 일반적으로 mixin function이라고 합니다. 객체 확장에 함수를 사용하는 방법의 가장 큰 이점은 private data를 클로져를 통해 캡슐화시킬 수 있다는 것입니다. 다르게 말하자면 private 상태를 지정할 수 있다는 의미입니다.

특정 함수를 통할 필요 없이 public 접근이 가능한 속성에 대해 접근 제한을 거는 것은 문제가 있습니다. 따라서 private 클로져에 속성 값을 숨겨야 하며 이는 아래와 같이 구현합니다.

```javascript
// import Events from 'eventemitter3';

const rawMixin = function () {
    const attrs = {};
    return Object.assign(
        this,
        {
            set(name, value) {
                attrs[name] = value;
                this.emit("change", {
                    prop: name,
                    value: value,
                });
            },
            get(name) {
                return attrs[name];
            },
        },
        Events.prototype
    );
};

const mixinModel = (target) => rawMixin.call(target);
const george = { name: "george" };
const model = mixinModel(george);
model.on("change", (data) => console.log(data));
model.set("name", "Sam");
/*
{
    prop: 'name',
    value: 'Sam'
}
*/
```

`attrs`를 public 속성에서 private 영역으로 옮겨서 public API를 통한 접근을 차단할 수 있습니다. 접근할 수 있는 유일한 방법은 privileged method뿐입니다. Privileged method는 클로져 영역에 정의된 함수로 private data에 접근 가능한 함수들을 일컫습니다.

위 예제를 보면 mixin function `rawMixin()`에 대한 wrapper로 `mixinModel()`을 선언한 것을 알 수 있습니다. 이는 예제에서 `Function.prototype.call()`을 사용했듯이 함수 내에서 `this`의 값을 설정해야 하기 때문입니다. Wrapper를 생략하고 호출자가 알아서 하도록 놔둘 수 있지만 그럴 경우 혼동될 가능성이 있습니다.

<br>

## Reference

https://developer.mozilla.org/ko/docs/Web/JavaScript/Inheritance_and_the_prototype_chain
