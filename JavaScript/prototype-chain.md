# 프로토타입 체인(Prototype Chain)

## 프로토타입 체인이란

JavaScript는 특정 객체의 프로퍼티나 메소드에 접근하려고 할 때, 해당 객체에 접근하려는 프로퍼티나 메소드가 없다면 [[Prototype]]이 가리키는 링크를 따라 자신의 부모 역할을 하는 프로토타입 객체의 프로퍼티나 메소드를 차례대로 검색합니다. 이것을 프로토타입 체인이라고 합니다.

```javascript
const student = {
    name: "Lee",
    score: 90,
};

// Object.prototype.hasOwnProperty()
console.log(student.hasOwnProperty("name")); // true
console.log(student.__proto__ === Object.prototype); // true
console.log(Object.prototype.hasOwnProperty("hasOwnProperty")); // true
```

student 객체는 `hasOwnProperty()` 메소드를 가지고 있지 않으므로 에러가 발생하여야 하나 정상적으로 결과가 출력됩니다. 이는 student 객체의 [[Prototype]]이 가리키는 링크를 따라가서 student 객체의 부모 역할을 하는 프로토타입 객체(Object.prototype)의 메소드 `hasOwnProperty()`를 호출하였기 때문에 가능한 것입니다.

<br>

## 객체를 생성하는 여러 방법과 프로토타입 체인 결과

### 문법 생성자로 객체 생성

객체 리터럴 방식으로 생성된 객체는 결국 내장 함수(built-in)인 `Object()` 생성자 함수로 객체를 생성하는 것을 단순화시킨 것입니다. `Object()` 생성자 함수는 물론 함수입니다. 따라서 함수 객체인 `Object()` 생성자 함수는 일반 객체와 달리 `prototype` 프로퍼티가 존재합니다.

결론적으로 객체 리터럴을 사용하여 객체를 생성한 경우, 그 객체의 프로토타입 객체는 Object.prototype입니다.

```javascript
var o = { a: 1 };
// o 객체는 프로토타입으로 Object.prorotype을 가집니다.
// 이로 인해 o.hasOwnProperty('a') 같은 코드를 사용할 수 있습니다. hasOwnProperty라는 속성은 Object.prototype의 속성입니다.
// Object.prototype의 프로토타입은 null입니다.
// o ---> Object.prototype ---> null

var a = ["yo", "whadup", "?"];
// a ---> Array.prototype ---> Object.prototype ---> null

function f() {
    return 2;
}
// f ---> Function.prototype ---> Object.prototype ---> null

var person = {
    name: "Lee",
    gender: "male",
    sayHello: function () {
        console.log(`Hi! My name is ${this.name}`);
    },
};

console.log(person.__proto__ === Object.prototype); // ① true
console.log(Object.prototype.constructor === Object); // ② true
console.log(Object.__proto__ === Function.prototype); // ③ true
console.log(Function.prototype.__proto__ === Object.prototyp); // ④ true
```

<p align="center">
    <img width="400" alt="Object literal prototype chaining" src="https://user-images.githubusercontent.com/22341362/146633153-87686a77-78df-4385-ba24-d3ec7f61a9e0.png">
</p>

### 생성자를 이용

생성자 함수로 객체를 생성하기 위해서는 우선 생성자 함수를 정의하여야 합니다.

함수를 정의하는 방식은 3가지가 있습니다.

1. 함수선언식(function declaration)
2. 함수표현식(function expression)
3. `Function()` 생성자 함수

함수표현식으로 함수를 정의할 때 함수 리터럴 방식을 사용합니다. 함수선언식의 경우, JavaScript 엔진이 내부적으로 기명 함수표현식으로 변환합니다. 결국 함수선언식, 함수표현식 모두 함수 리터럴 방식을 사용합니다. 함수 리터럴 방식은 `Function()` 생성자 함수로 함수를 생성하는 것을 단순화시킨 것입니다.

즉, 3가지 함수 정의 방식은 결국 `Function()` 생성자 함수를 통해 함수 객체를 생성합니다. 따라서 어떠한 방식으로 함수 객체를 생성하여도 모든 함수 객체의 prototype은 Function.prototype입니다. 생성자 함수도 함수 객체이므로 생성자 함수의 prototype도 Function.prototype입니다.

```javascript
function Graph() {
    this.vertexes = [];
    this.edges = [];
}

Graph.prototype = {
    addVertex: function (v) {
        this.vertexes.push(v);
    },
};

var g = new Graph();
// g는 'vertexes'와 'edges'를 속성으로 가지는 객체입니다.
// g.[[Prototype]]은 Graph.prototype과 같은 값을 가집니다.

function Person(name, gender) {
    this.name = name;
    this.gender = gender;
    this.sayHello = function () {
        console.log(`Hi! My name is ${this.name}`);
    };
}

const foo = new Person("Lee", "male");

console.log(foo.__proto__ === Person.prototype); // ① true
console.log(Person.prototype.__proto__ === Object.prototype); // ② true
console.log(Person.prototype.constructor === Person); // ③ true
console.log(Person.__proto__ === Function.prototype); // ④ true
console.log(Function.prototype.__proto__ === Object.prototype); // ⑤ true
```

<p align="center">
    <img width="400" alt="Constructor function prototype chaining" src="https://user-images.githubusercontent.com/22341362/146633570-3a088f0c-572f-4aa3-89b8-7e6179bb2462.png">
</p>

객체 리터럴 방식이나 생성자 함수 방식이나 결국은 모든 객체의 부모 객체인 Object.prototype 객체에서 프로토타입 체인이 끝납니다. 이때 Object.prototype 객체를 **프로토타입 체인의 종점(end of prototype chain)**이라고 합니다.

### `Object.create()` 이용

ECMAScript 5부터 `Object.create()` 메소드를 호출하여 새로운 객체를 만들 수 있습니다. 생성된 객체의 프로토타입은 이 메소드의 첫 번째 인수로 지정됩니다.

```javascript
var a = { a: 1 };
// a ---> Object.prototype ---> null

var b = Object.create(a);
// b ---> a ---> Object.prototype ---> null

var c = Object.create(b);
// c ---> b ---> a ---> Object.prototype ---> null

var d = Object.create(null);
// d ---> null
console.log(d.hasOwnProperty); // undefined. d는 Object.prototype을 상속받지 않았기 때문입니다.
```

<br>

## 원시 타입의 확장

JavaScript에서 원시 타입을 제외한 모든 것은 객체입니다. 그런데 아래 예제를 살펴보면 원시 타입인 문자열이 객체와 유사하게 동작합니다.

```javascript
const str = "test";
console.log(typeof str); // string
console.log(str.constructor === String); // true
console.dir(str); // test

const strObj = new String("test");
console.log(typeof strObj); // object
console.log(strObj.constructor === String); // true
console.dir(strObj); // { 0: "t", 1: "e", 2: "s", 3: "t", length: 4, [[Prototype]]: String, [[PrimitiveValue]]: "test" }

console.log(str.toUpperCase()); // TEST
console.log(strObj.toUpperCase()); // TEST
```

원시 타입 문자열과 `String()` 생성자 함수로 생성한 문자열 객체의 타입은 분명히 다릅니다. 원시 타입은 객체가 아니므로 프로퍼티나 메소드를 가질 수 없습니다. 하지만 원시 타입으로 프로퍼티나 메소드를 호출하면 원시 타입과 연관된 객체로 일시적으로 변환되어 프로토타입 객체를 공유하게 됩니다.

원시 타입은 객체가 아니므로 프로퍼티나 메소드를 직접 추가할 수 없습니다.

```javascript
const str = "test";

// 에러가 발생하지 않습니다.
str.myMethod = function () {
    console.log("str.myMethod");
};

str.myMethod(); // Uncaught TypeError: str.myMethod is not a function
```

하지만 String 객체의 프로토타입 객체 String.prototype에 메소드를 추가하면 원시 타입, 객체 모두 메소드를 사용할 수 있습니다.

```javascript
const str = "test";

String.prototype.myMethod = function () {
    return "myMethod";
};

console.log(str.myMethod()); // myMethod
console.log("string".myMethod()); // myMethod

console.log(str.__proto__ === String.prototype); // ① true
console.log(String.prototype.__proto__ === Object.prototype); // ② true
console.log(String.prototype.constructor === String); // ③ true
console.log(String.__proto__ === Function.prototype); // ④ true
console.log(Function.prototype.__proto__ === Object.prototype); // ⑤ true
```

<p align="center">
    <img width="400" alt="String constructor prototype chaining" src="https://user-images.githubusercontent.com/22341362/146634216-1772ddeb-6986-40fd-938f-352aa42b8f48.png">
</p>

<br>

## 프로토타입 객체의 변경

객체를 생성할 때 프로토타입은 결정됩니다. 결정된 프로토타입 객체는 다른 임의의 객체로 변경할 수 있습니다. 이것은 부모 객체인 프로토타입을 동적으로 변경할 수 있다는 것을 의미합니다. 이러한 특징을 활용하여 객체의 상속을 구현할 수 있습니다.

이때 주의할 것은 프로토타입을 변경하면

-   프로토타입 객체 변경 시점 이전에 생성된 객체는 기존 프로토타입 객체를 [[Prototype]]에 바인딩합니다.
-   프로토타입 객체 변경 시점 이후에 생성된 객체는 변경된 프로토타입 객체를 [[Prototype]]에 바인딩합니다.

```javascript
function Person(name) {
    this.name = name;
}

const foo = new Person("Lee");

Person.prototype = { gender: "male" };

const bar = new Person("Kim");

console.log(foo.gender); // undefined
console.log(bar.gender); // male

console.log(foo.constructor); // ① Person(name)
console.log(bar.constructor); // ② Object()
```

<p align="center">
    <img width="700" alt="Changing prototype" src="https://user-images.githubusercontent.com/22341362/146634480-76724c96-f1d4-4f60-a81d-3cfa71cd5f7e.png">
</p>

① `constructor` 프로퍼티는 `Person()` 생성자 함수를 가리킵니다.
② 프로토타입 객체 변경 후, `Person()` 생성자 함수의 `prototype` 프로퍼티가 가리키는 프로토타입 객체를 일반 객체로 변경하면서 `Person.prototype.constructor` 프로퍼티도 삭제되었습니다. 따라서 `bar.constructor`의 값은 프로토타입 체이닝에 의해 `Object.prototype.constructor` 즉, `Object()` 생성자 함수가 됩니다.

<br>

## Reference

-   [PoiemaWeb - "5.14 프로토타입"](https://poiemaweb.com/js-prototype)
-   [MDN Web Docs - "상속과 프로토타입"](https://developer.mozilla.org/ko/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
