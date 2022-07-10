# Prototype Pattern

> 동일한 타입의 여러 객체 간에 프로퍼티 공유

prototype pattern은 동일한 타입의 여러 객체 간에 프로퍼티를 공유하는 유용한 방법입니다. 프로토타입은 자바스크립트의 고유한 객체이며, 프로토타입 체인을 통해 객체에서 접근할 수 있습니다.

애플리케이션에서는 종종 동일한 타입의 객체를 여러 개 만들어야 합니다. 이를 수행하는 유용한 방법은 ES6 클래스의 여러 인스턴스를 만드는 것입니다.

개를 여러 개 만들고 싶다고 해봅시다! 예시 속 개는 많은 것을 하지는 않습니다: 단순히 이름을 가지고 짖을 수 있습니다!

```js
class Dog {
  constructor(name) {
    this.name = name;
  }

  bark() {
    return `Woof!`;
  }
}

const dog1 = new Dog('Daisy');
const dog2 = new Dog('Max');
const dog3 = new Dog('Spot');
```

여기에서 `constructor`가 `name` 프로퍼티를 포함하고 클래스 자체가 `bark` 프로퍼티를 포함하는 방법에 주목해 봅시다. ES6 클래스를 사용할 때, 클래스 자체에 정의된 모든 프로퍼티 (이 경우에는 `bark`) 는 자동으로 `prototype`에 추가됩니다.

생성자의 `prototype` 프로퍼티에 접근하거나 인스턴스의 `__proto__` 프로퍼티를 통해 `prototype`을 직접 볼 수 있습니다.

```js
console.log(Dog.prototype);
// constructor: ƒ Dog(name, breed) bark: ƒ bark()

console.log(dog1.__proto__);
// constructor: ƒ Dog(name, breed) bark: ƒ bark()
```

생성자의 모든 인스턴스에서 `__proto__` 값은 생성자의 프로토타입에 대한 직접 참조입니다! 객체에 직접적으로 존재하지 않는 객체의 프로퍼티에 접근하려고 할 때마다 자바스크립트는 프로토타입 체인 내에서 해당 프로토타입을 사용할 수 있는지 확인하기 위해 프로토타입 체인으로 이동합니다.

![](https://user-images.githubusercontent.com/22341362/176063074-d0265dce-06c6-4af7-bb99-fa85a18da999.png)

prototype pattern은 동일한 프로퍼티에 접근해야 하는 객체로 작업할 때 매우 강력합니다. 매번 프로퍼티를 복제하는 대신, 모든 인스턴스가 프로토타입 객체에 접근할 수 있으므로 프로토타입에 프로퍼티를 추가하기만 하면 됩니다.

모든 인스턴스가 프로토타입에 접근할 수 있으므로 인스턴스를 생성한 후에도 프로토타입에 프로퍼티를 쉽게 추가할 수 있습니다.

개가 짖을 수 있을 뿐만 아니라 놀 수도 있다고 해봅시다! 우리는 프로토타입에 `play` 프로퍼티를 추가하여 이를 가능하게 할 수 있습니다.

```js
class Dog {
  constructor(name) {
    this.name = name;
  }

  bark() {
    return `Woof!`;
  }
}

const dog1 = new Dog('Daisy');
const dog2 = new Dog('Max');
const dog3 = new Dog('Spot');

Dog.prototype.play = () => console.log('Playing now!');

dog1.play();
```

프토토타입 체인이라는 용어는 하나 이상의 단계가 있을 수 있음을 나타냅니다. 물론! 지금까지는 `__proto__`가 참조하는 첫 번쨰 객체에서 직접 사용할 수 있는 프로퍼티에 접근하는 방법만 살펴보았습니다. 그러나 프로토타입도 자체적으로 `__proto__` 객체를 가지고 있습니다!

다른 유형의 개, 슈퍼개를 만들어 봅시다! 이 개는 `Dog`로부터 모든 것을 상속받고 날 수도 있어야 합니다. `Dog` 클래스를 확장하고 `fly` 메소드를 추가하여 슈퍼개를 만들 수 있습니다.

```js
class SuperDog extends Dog {
  constructor(name) {
    super(name);
  }

  fly() {
    return 'Flying!';
  }
}
```

`Daisy`라는 이름의 개를 만들고, 짖은 다음 날게 해봅시다!

```js
class Dog {
  constructor(name) {
    this.name = name;
  }

  bark() {
    console.log('Woof!');
  }
}

class SuperDog extends Dog {
  constructor(name) {
    super(name);
  }

  fly() {
    console.log(`Flying!`);
  }
}

const dog1 = new SuperDog('Daisy');
dog1.bark();
dog1.fly();
```

`Dog` 클래스를 확장했기 때문에 `bark` 메소드에 접근할 수 있습니다. `SuperDog`의 프로토타입의 `__proto__` 값은 `Dog.prototype` 객체를 가리킵니다!

![](https://user-images.githubusercontent.com/22341362/176064683-bf665550-435b-4967-a3dc-fbaae22a01da.png)

이제 왜 프로토타입 체인이라고 불리는지 이유가 명확해졌습니다: 객체에서 직접 사용할 수 없는 프로퍼티에 접근하려고 할 때, 자바스크립트는 프로퍼티를 찾을 때까지 `__proto__`가 가리키는 모든 객체를 재귀적으로 거슬러 올라갑니다!

---

## Object.create

`Object.create` 메소드를 사용하면 프로토타입 값을 명시적으로 전달할 수 있는 새 객체를 생성할 수 있습니다.

```js
const dog = {
  bark() {
    return `Woof!`;
  },
};

const pet1 = Object.create(dog);
```

`pet1` 자체에는 프로퍼티가 없지만 프로토타입 체인의 프로퍼티에는 접근할 수 있습니다! `dog` 객체를 `pet1`의 프로토타입으로 전달했기 때문에, `bark` 프로퍼티에 접근할 수 있습니다.

```js
const dog = {
  bark() {
    console.log(`Woof!`);
  },
};

const pet1 = Object.create(dog);

pet1.bark(); // Woof!
console.log('Direct properties on pet1: ', Object.keys(pet1));
console.log("Properties on pet1's prototype: ", Object.keys(pet1.__proto__));
```

`Object.create`는 새로 생성된 객체의 프로토타입을 명시하여 객체가 다른 객체의 프로퍼티를 직접 상속하도록 하는 간단한 방법입니다. 새 객체는 프로토타입 체인을 따라 이동하여 새 프로토타입에 접근할 수 있습니다.

---

prototype pattern을 사용하면 객체가 다른 객체의 프로퍼티에 쉽게 접근하고 상속할 수 있습니다. 프로토타입 체인을 사용하면 객체 자체에 직접 정의되어 있지 않은 프로퍼티에 접근할 수 있기 때문에, 메소드와 프로퍼티의 중복을 방지할 수 있고 결과적으로 사용되는 메모리의 양을 줄일 수 있습니다.
