# 클래스(Class)

JavaScript는 프로토타입 기반 (prototype-based) 객체 지향 언어입니다. 프로토타입 기반 프로그래밍은 클래스가 필요없는 (class-free) 객체 지향 프로그래밍 스타일로 프로토타입 체인과 클로저 등으로 객체 지향 언어의 상속, 캡슐화 (정보 은닉) 등의 개념을 구현할 수 있습니다.

ES5에서는 생성자 함수와 프로토타입, 클로저를 사용하여 객체 지향 프로그래밍을 구현하였습니다.

```javascript
var Person = (function () {
    // Constructor
    function Person(name) {
        this._name = name;
    }

    // public method
    Person.prototype.sayHi = function () {
        console.log("Hi! " + this._name);
    };

    // return constructor
    return Person;
})();

var me = new Person("Lee");
me.sayHi(); // Hi! Lee

console.log(me instanceof Person); // true
```

위 예제를 프로토타입 관점에서 표현해 보면 아래와 같습니다.

<p align="center">
    <img width="400" alt="Prototype-based OOP" src="https://user-images.githubusercontent.com/22341362/148758547-296db74f-9295-4efa-8cda-9ff56a6e6469.png">
</p>

ES6의 클래스는 기존 프로토타입 기반 객체 지향 프로그래밍보다 클래스 기반 언어에 익숙한 프로그래머가 보다 빠르게 학습할 수 있는 새로운 문법을 제시합니다. 그렇다고 ES6의 클래스가 기존의 프로토타입 기반 객체 지향 모델을 폐지하고 새로운 객체 지향 모델을 제공하는 것은 아닙니다. 사실 클래스도 함수이며 기존 프로토타입 기반 패턴의 문법적 설탕(syntactic sugar)이라고 볼 수 있습니다. 다만, 클래스는 생성자 함수와 정확히 동일하게 동작하지 않고, 보다 엄격합니다. 따라서 클래스를 프로토타입 기반 패턴의 문법적 설탕이라고 인정하지 않는 견해도 일리가 있습니다.

## 클래스 정의

ES6 클래스는 `class` 키워드를 사용하여 정의합니다.

클래스 이름은 생성자 함수와 마찬가지로 Pascal case를 사용하는 것이 일반적입니다. Pascal case를 사용하지 않아도 에러가 발생하지는 않습니다.

```javascript
// 클래스 선언문
class Person {
    // Constructor
    constructor(name) {
        this._name = name;
    }

    sayHi() {
        console.log(`Hi! ${this._name}`);
    }
}

// 인스턴스 생성
const me = new Person("Lee");
me.sayHi(); // Hi! Lee

console.log(me instanceof Person); // true
```

### 클래스의 호이스팅

클래스는 클래스 선언문 이전에 참조할 수 없습니다.

```javascript
console.log(Foo); // Uncaught ReferenceError: Cannot access 'Foo' before initialization

class Foo {}
```

하지만 호이스팅이 발생하지 않는 것은 아닙니다. 클래스는 `var` 키워드로 선언한 변수처럼 호이스팅되지 않고, `let`, `const` 키워드로 선언한 변수처럼 호이스팅됩니다. 따라서 클래스 선언문 이전에 일시적 사각지대(TDZ, Temporal Dead Zone)에 빠지기 때문에 호이스팅이 발생하지 않는 것처럼 동작합니다.

```javascript
const Foo = "";

{
    // 호이스팅이 발생하지 않는다면 ""가 출력되어야 합니다.
    console.log(Foo); // Uncaught ReferenceError: Cannot access 'Foo' before initialization
    class Foo {}
}
```

클래스 선언문도 변수 선언, 함수 정의와 마찬가지로 호이스팅이 발생합니다. 호이스팅은 `var`, `let`, `const`, `function`, `function*`, `class` 키워드를 사용한 모든 선언문에 적용됩니다. 다시 말해, 선언문을 통해 선언된 모든 식별자 (변수, 함수, 클래스 등) 은 호이스팅됩니다. 모든 선언문은 런타임 이전에 먼저 실행되기 때문입니다.

### 표현식으로 클래스 정의하기

일반적이지는 않지만, 표현식으로도 클래스를 정의할 수 있습니다. 함수와 마찬가지로 클래스는 이름을 가질 수도 갖지 않을 수도 있습니다. 이때 클래스가 할당된 변수를 사용해 클래스를 생성하지 않고 기명 클래스의 클래스 이름을 사용해 클래스를 생성하면 에러가 발생합니다. 이는 함수와 마찬가지로 클래스 표현식에서 사용한 클래스 이름은 외부 코드에서 접근이 불가능하기 때문입니다.

```javascript
// 클래스명 MyClass는 함수 표현식과 동일하게 클래스 몸체 내부에서만 유효한 식별자입니다.
const Foo = class MyClass {};

const foo = new Foo();
console.log(foo); // MyClass {}

new MyClass(); // Uncaught ReferenceError: MyClass is not defined
```

<br>

## 인스턴스 생성

생성자 함수와 마찬가지로 `new` 연산자와 함께 클래스 이름을 호출하면 클래스의 인스턴스가 생성됩니다.

```javascript
class Foo {}

const foo = new Foo();
```

위 코드에서 `new` 연산자와 함께 호출한 `Foo`는 클래스의 이름이 아니라 constructor(생성자)입니다. 표현식이 아닌 선언식으로 정의한 클래스의 이름은 constructor와 동일합니다.

```javascript
console.log(Object.getPrototypeOf(foo).constructor === Foo); // true
```

`new` 연산자를 사용하지 않고 constructor를 호출하면 TypeError가 발생합니다. Constructor는 `new` 연산자 없이 호출할 수 없습니다.

```javascript
class Foo {}

const foo = Foo(); // Uncaught TypeError: Class constructor Foo cannot be invoked without 'new'
```
