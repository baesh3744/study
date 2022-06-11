# 프로토타입(Prototype)

## 프로토타입이란

Java, C++와 같은 클래스 기반 객체지향 프로그래밍 언어와 달리 JavaScript는 프로토타입 기반 객체지향 프로그래밍 언어입니다.

클래스 기반 객체지향 프로그래밍 언어는 객체 생성 이전에 클래스를 정의하고 이를 통해 객체 (인스턴스) 를 생성합니다. 하지만 프로토타입 기반 객체지향 프로그래밍 언어는 클래스 없이도 객체를 생성할 수 있습니다 (ECMAScript 6에서 클래스가 추가되었습니다).

JavaScript의 모든 객체는 자신의 부모 역할을 담당하는 객체와 연결되어 있습니다. 이것은 마치 객체지향의 상속 개념과 같이 부모 객체의 프로퍼티 또는 메소드를 상속받아 사용할 수 있게 합니다. 이러한 부모 객체를 **프로토타입 객체** 또는 줄여서 **프로토타입**이라고 합니다.

프로토타입 객체는 생성자 함수에 의해 생성된 각각의 객체에 공유 프로퍼티를 제공하기 위해 사용합니다.

```javascript
const student = {
    name: "Lee",
    score: 90,
};

// student에는 hasOwnProperty() 메소드가 없지만 아래 구문은 정상 동작합니다.
console.log(student.hasOwnProperty("name")); // true
```

<p align="center">
    <img width="344" alt="스크린샷 2021-12-18 오후 3 23 34" src="https://user-images.githubusercontent.com/22341362/146631680-e4ab5212-539a-41f3-b505-1298a0a2422e.png">
</p>
<p align="center">Google Chrome에서 student 객체 출력 결과</p>

ECMAScript spec에는 다음과 같이 적혀있습니다.

> JavaScript의 모든 객체는 [[Prototype]]이라는 인터널 슬롯(internal slot)을 가집니다. [[Prototype]]의 값은 `null` 또는 객체이며 상속을 구현하는데 사용됩니다. [[Prototype]] 객체의 데이터 프로퍼티는 get 엑세스를 위해 상속되어 자식 객체의 프로퍼티처럼 사용할 수 있습니다. 하지만 set 엑세스는 허용되지 않습니다.

[[Prototype]]의 값은 프로토타입 객체이며 `__proto__` accessor property로 접근할 수 있습니다. `__proto__` 프로퍼티에 접근하면 내부적으로 `Object.getPrototypeOf()`가 호출되어 프로터타입 객체를 반환합니다.

```javascript
console.log(student.__proto__ === Object.prototype); // true
```

객체를 생성할 때 프로토타입은 결정됩니다. 결정된 프로토타입 객체는 다른 임의의 객체로 변경할 수 있습니다. 이것은 부모 객체인 프로토타입을 동적으로 변경할 수 있다는 것을 의미합니다. 이러한 특징을 활용하여 객체의 상속을 구현할 수 있습니다.

<br>

## [[Prototype]] vs. prototype 프로퍼티

모든 객체는 자신의 프로토타입 객체를 가리키는 [[Prototype]] 인터널 슬롯을 가지며 상속을 위해 사용됩니다.

함수도 객체이므로 [[Prototype]] 인터널 슬롯을 가집니다. 그런데 함수 객체는 일반 객체와 달리 `prototype` 프로퍼티도 가집니다.

```javascript
function Person(name) {
    this.name = name;
}

const foo = new Person("Lee");

console.dir(Person); // prototype 프로퍼티가 있습니다.
console.dir(foo); // prototype 프로퍼티가 없습니다.
```

-   [[Prototype]]

    -   함수를 포함한 모든 객체가 가지고 있는 인터널 슬롯입니다.
    -   객체의 입장에서 자신의 부모 역할을 하는 프로토타입 객체를 가리키며, 함수 객체의 경우 `Function.prototype`을 가리킵니다.

    ```javascript
    console.log(Person.__proto__ === Function.prototype); // true
    ```

-   `prototype` 프로퍼티

    -   함수 객체만 가지고 있는 프로퍼티입니다.
    -   함수 객체가 생성자로 사용될 때 이 함수를 통해 생성될 객체의 부모 역할을 하는 객체(프로토타입 객체)를 가리킵니다.

    ```javascript
    console.log(Person.prototype === foo.__proto__); // true
    ```

<br>

## `constructor` 프로퍼티

프로토타입 객체는 `constructor` 프로퍼티를 갖습니다. 이 `constructor` 프로퍼티는 객체의 입장에서 자신을 생성한 객체를 가리킵니다.

예를 들어, `Person()` 생성자 함수에 의해 생성된 객체를 `foo`라고 가정해 봅시다. 이때, `foo` 객체 입장에서 자신을 생성한 객체는 `Person()` 생성자 함수이며, `foo` 객체의 프로토타입 객체는 `Person.prototype`입니다. 따라서 `Person.ptototype`의 `constructor` 프로퍼티는 `Person()` 생성자 함수를 가리킵니다.

```javascript
function Person(name) {
    this.name = name;
}

const foo = new Person("Lee");

// Person() 생성자 함수에 의해 생성된 객체를 생성한 객체는 Person() 생성자 함수입니다.
console.log(Person.prototype.constructor === Person); // true

// foo 객체를 생성한 객체는 Person() 생성자 함수입니다.
console.log(foo.constructor === Person); // true

// Person() 생성자 함수를 생성한 객체는 Function() 생성자 함수입니다.
console.log(Person.constructor === Function); // true
```

<br>

## Reference

-   [PoiemaWeb - "5.14 프로토타입"](https://poiemaweb.com/js-prototype)
