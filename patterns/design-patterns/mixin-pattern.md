# Mixin Pattern

> 객체나 클래스에 상속 없이 기능 추가

**믹스인**은 재사용 가능한 기능을 상속 없이 다른 객체나 클래스에 추가하기 위해 사용하는 객체입니다. 믹스인은 단독으로 사용할 수 없습니다: 믹스인의 유일한 목적은 객체나 클래스에 상속 없이 *기능을 추가*하는 것입니다.

애플리케이션에서 dog를 여러 개 생성해야 한다고 가정해 보겠습니다. 그러나 우리가 생성한 기본 dog에는 `name` 프로퍼티를 외에는 프로퍼티가 없습니다.

```js
class Dog {
  constructor(name) {
    this.name = name;
  }
}
```

Dog는 이름을 갖는 것 이상을 할 수 있어야 합니다. 짖을 수 있어야 하고 꼬리를 흔들 수 있어야 하고 놀 수 있어야 합니다! 이것들을 `Dog`에 직접 추가하는 대신, `bark`, `wagTail`, `play` 프로퍼티를 제공해주는 믹스인을 만들 수 있습니다.

```js
const dogFunctionality = {
  bark: () => console.log("Woof!"),
  wagTail: () => console.log("Wagging my tail!"),
  play: () => console.log("Playing!"),
};
```

`Object.assign` 메소드를 사용하여 `Dog` 프로토타입에 `dogFunctionality` 믹스인을 추가할 수 있습니다. 이 메소드를 사용하면 *타겟 객체*에 프로퍼티를 추가할 수 있습니다: 이 경우에는 `Dog.prototype`입니다. `Dog`의 프로토타입에 `dogFunctionality`의 프로퍼티를 추가했기 때문에 `Dog`의 새 인스턴스에서 이 프로퍼티에 접근할 수 있습니다!

```js
class Dog {
  constructor(name) {
    this.name = name;
  }
}

const dogFunctionality = {
  bark: () => console.log("Woof!"),
  wagTail: () => console.log("Wagging my tail!"),
  play: () => console.log("Playing!"),
};

Object.assign(Dog.prototype, dogFunctionality);
```

이름이 Daisy인 첫 번째 펫 `pet1`을 생성해 보겠습니다. `Dog`의 프로토타입에 `dogFunctionality` 믹스인을 추가했기 때문에 Daisy는 걷고, 꼬리를 흔들고, 놀 수 있어야 합니다!

```js
const pet1 = new Dog("Daisy");

pet1.name; // Daisy
pet1.bark(); // Woof!
pet1.play(); // Playing!
```

믹스인을 사용하면 클래스나 객체에 상속 없이 커스텀 기능을 쉽게 추가할 수 있습니다.

---

믹스인을 사용하면 상속 없이 기능을 추가할 수 있지만, 믹스인 자체는 상속을 사용할 수 있습니다!

대부분의 포유류 (돌고래를 제외하고.. 어쩌면 다른 것들도..) 는 걷고 잘 수 있습니다. 개는 포유류이기 때문에 걷고 잘 수 있어야 합니다!

`walk`와 `sleep` 프로퍼티를 추가해주는 `animalFunctionality` 믹스인을 생성해 보겠습니다.

```js
const animalFunctionality = {
  walk: () => console.log("Walking!"),
  sleep: () => console.log("Sleeping!"),
};
```

`Object.assign`을 사용해서 `dogFunctionality` 프로토타입에 이 프로퍼티를 추가할 수 있습니다. 이 경우에 타겟 객체는 `dogFunctionality`입니다.

```js
const animalFunctionality = {
  walk: () => console.log("Walking!"),
  sleep: () => console.log("Sleeping!"),
};

const dogFunctionality = {
  bark: () => console.log("Woof!"),
  wagTail: () => console.log("Wagging my tail!"),
  play: () => console.log("Playing!"),
  walk() {
    super.walk();
  },
  sleep() {
    super.sleep();
  },
};

Object.assign(dogFunctionality, animalFunctionality);
Object.assign(Dog.prototype, dogFunctionality);
```

이제 `Dog`의 새 인스턴스는 `walk`와 `sleep` 메소드에 접근할 수 있습니다.

```js
// index.js
class Dog {
  constructor(name) {
    this.name = name;
  }
}

const animalFunctionality = {
  walk: () => console.log("Walking!"),
  sleep: () => console.log("Sleeping!"),
};

const dogFunctionality = {
  __proto__: animalFunctionality,
  bark: () => console.log("Woof!"),
  wagTail: () => console.log("Wagging my tail!"),
  play: () => console.log("Playing!"),
  walk() {
    super.walk();
  },
  sleep() {
    super.sleep();
  },
};

Object.assign(Dog.prototype, dogFunctionality);

const pet1 = new Dog("Daisy");

console.log(pet1.name);
pet1.bark();
pet1.play();
pet1.walk();
pet1.sleep();
```

---

믹스인의 예는 브라우저 환경의 `Window` 인터페이스에서 볼 수 있습니다. `Window` 객체는 프로퍼티의 많은 부분을 `WindowOrWorkerGlobalScope`와 `WindowEventHandlers` 믹스인에서 구현하고, 이들 덕분에 `setTimeout`, `setInterval`, `indexedDB`, `isSecureContext`와 같은 프로퍼티에 접근할 수 있습니다.

이들은 믹스인이므로 객체에 기능을 추가하는 데만 사용되고 `WindowOrWorkerGlobalScope` 타입의 객체를 생성할 수 없습니다.

```js
// index.js
window.indexedDB.open("toDoList");

window.addEventListener("beforeunload", (event) => {
  event.preventDefault();
  event.returnValue = "";
});

window.onbeforeunload = function () {
  console.log("Unloading!");
};

console.log(
  "From WindowEventHandlers mixin: onbeforeunload",
  window.onbeforeunload
);

console.log(
  "From WindowOrWorkerGlobalScope mixin: isSecureContext",
  window.isSecureContext
);

console.log(
  "WindowEventHandlers itself is undefined",
  window.WindowEventHandlers
);

console.log(
  "WindowOrWorkerGlobalScope itself is undefined",
  window.WindowOrWorkerGlobalScope
);
```

---

### React (pre ES6)

믹스인은 ES6 클래스가 도입되기 전에 React 컴포넌트에 기능을 추가하는데 자주 사용되었습니다. 믹스인은 컴포넌트에 불필요한 복잡성을 추가하여 유지 관리 및 재사용을 어렵게 만들기 때문에 React 팀은 [믹스인 사용을 권장하지 않습니다.](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html) 대신, [고차 컴포넌트를 사용하도록 권장했습니다.](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750) (이제는 Hooks로 대체 가능)

---

믹스인을 사용하면 객체의 프로토타입에 기능을 주입함으로써 상속 없이 객체에 기능을 쉽게 추가할 수 있습니다. 객체의 프로토타입을 수정하는 것은 프로토타입 오염과 함수의 기원에 대한 불확실성을 야기하기 때문에 나쁜 습관으로 간주됩니다.
