# Proxy Pattern

> 타겟 객체애 대한 상호작용 가로채기 및 제어하기

프록시 객체를 사용하면 특정 객체와의 상호작용을 더 제어할 수 있습니다. 프록시 객체는 객체와 상호작용할 때 (e.g. 값을 얻거나 값을 설정할 때) 의 동작을 결정할 수 있습니다.

---

일반적으로 프록시는 누군가의 대리인을 의미합니다. 그 사람에게 직접 말하는 대신에 프록시에게 말을 하고, 프록시가 원래 연락을 시도한 사람에게 전달합니다. JavaScript에서도 동일한 일이 발생합니다: 타겟 객체와 직접 상호작용하는 대신에 프록시 객체와 상호작용합니다.

---

John Doe를 나타내는 `person` 객체를 생성해보겠습니다.

```js
const person = {
  name: 'John Doe',
  age: 42,
  nationality: 'American',
};
```

우리는 이 객체와 직접 상호작용하는 대신, 프록시 객체와 상호작용하기를 원합니다. JavaScript에서는 `Proxy`의 새 인스턴스를 생성함으로써 쉽게 새 프록시를 생성할 수 있습니다.

```js
const person = {
  name: 'John Doe',
  age: 42,
  nationality: 'American',
};

const personProxy = new Proxy(person, {});
```

`Proxy`의 두 번째 인수는 핸들러를 나타내는 객체입니다. 핸들러 객체 안에서 상호작용의 타입에 따른 동작을 정의할 수 있습니다. 프록시 핸들러에 추가할 수 있는 [메소드](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)는 많지만, `get`과 `set`이 가장 많이 사용됩니다:

- `get`: 프로퍼티에 **접근**하려 할 때 호출됩니다.

- `set`: 프로퍼티를 **수정**하려 할 때 호출됩니다.

결국 일어날 일은 다음과 같습니다:

![](https://user-images.githubusercontent.com/22341362/173809120-c2dd02d5-2912-497f-9671-daa2229061d1.png)

`person` 객체와 직접 상호작용하는 대신, `personProxy`와 상호작용합니다.

`personProxy` 프록시에 핸들러를 추가해 보겠습니다. 우리는 프로퍼티를 수정하려고 할 때 즉, `Proxy`의 `set` 메소드를 호출할 때, 프록시가 프로퍼티의 이전 값과 새로운 값을 기록하기를 원합니다. 우리는 프로퍼티에 접근하려고 할 때 즉, `Proxy`의 `get` 메소드를 호출할 때, 프록시가 프로퍼티의 키와 값을 포함하는 문장을 기록하기를 원합니다.

```js
const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    console.log(`The value of ${prop} is ${obj[prop]}`);
  },
  set: (obj, prop, value) => {
    console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
    obj[prop] = value;
  },
});
```

프로퍼티를 수정하거나 가져올 때 어떤 일이 발생하는지 살펴보겠습니다.

```js
const person = {
  name: 'John Doe',
  age: 42,
  nationality: 'American',
};

const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    console.log(`The value of ${prop} is ${obj[prop]}`);
  },
  set: (obj, prop, value) => {
    console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
    obj[prop] = value;
    return true;
  },
});

personProxy.name;
personProxy.age = 43;
```

`name` 프로퍼티에 접근할 때, 프록시는 문장을 반환했습니다: `The value of name is John Doe.`

`age` 프로퍼티를 수정할 때, 프록시는 이 프로퍼티의 이전 값과 새로운 값을 반환했습니다: `Changed age from 42 to 43`

---

프록시는 유효성 검사를 추가하는데 유용할 수 있습니다. 사용자는 `person`의 나이를 문자열로 변경하거나 빈 이름을 지정할 수 없어야 합니다. 또는 사용자가 객체에 존재하지 않는 프로퍼티에 접근하려고 하면, 사용자에게 알려야 합니다.

```js
const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    if (!obj[prop]) {
      console.log(`Hmm.. this property doesn't seem to exist on the target object`);
    } else {
      console.log(`The value of ${prop} is ${obj[prop]}`);
    }
  },
  set: (obj, prop, value) => {
    if (prop === 'age' && typeof value !== 'number') {
      console.log(`Sorry, you can only pass numeric values for age.`);
    } else if (prop === 'name' && value.length < 2) {
      console.log(`You need to provide a valid name.`);
    } else {
      console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
      obj[prop] = value;
    }
  },
});
```

잘못된 값을 전달하려고 할 때 어떤 일이 발생하는지 살펴보겠습니다.

```js
const person = {
  name: 'John Doe',
  age: 42,
  nationality: 'American',
};

const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    if (!obj[prop]) {
      console.log(`Hmm.. this property doesn't seem to exist`);
    } else {
      console.log(`The value of ${prop} is ${obj[prop]}`);
    }
  },
  set: (obj, prop, value) => {
    if (prop === 'age' && typeof value !== 'number') {
      console.log(`Sorry, you can only pass numeric values for age.`);
    } else if (prop === 'name' && value.length < 2) {
      console.log(`You need to provide a valid name.`);
    } else {
      console.log(`Changed ${prop} from ${obj[prop]} to ${value}.`);
      obj[prop] = value;
    }
    return true;
  },
});

personProxy.nonExistentProperty;
personProxy.age = '44';
personProxy.name = '';
```

프록시는 잘못된 값으로 `person` 객체를 수정하지 않도록 하여 데이터를 순수하게 유지하는데 도움을 줍니다.

---

### Reflect

JavaScript는 프록시로 작업할 때 타겟 객체를 더 쉽게 조작할 수 있게 해주는 `Reflect`라는 내장 객체를 제공합니다.

이전에는 프록시 내에서 대괄호 표기법으로 값을 직접 가져오거나 설정하여 타겟 객체의 프로퍼티를 수정하거나 접근하려고 했습니다. 대신에 `Reflect` 객체를 사용할 수 있습니다. `Reflect` 객체의 메소드는 `handler` 객체의 메소드와 동일한 이름을 가집니다.

`obj[prop]`을 통해 프로퍼티에 접근하거나 `obj[prop] = value`를 통해 프로퍼티를 설정하는 대신, `Reflect.get()` 및 `Reflect.set()`을 통해 타겟 객체의 프로퍼티에 접근하거나 수정할 수 있습니다. 해당 메소드들은 핸들러 객체의 메소드와 동일한 인수를 받습니다.

```js
const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    console.log(`The value of ${prop} is ${obj[prop]}`);
  },
  set: (obj, prop, value) => {
    console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
    Reflect.set(obj, prop, value);
  },
});
```

`Reflect` 객체를 사용해서 더 쉽게 타겟 객체의 프로퍼티에 접근하거나 수정할 수 있습니다.

```js
const person = {
  name: 'John Doe',
  age: 42,
  nationality: 'American',
};

const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    console.log(`The value of ${prop} is ${Reflect.get(obj, prop)}`);
  },
  set: (obj, prop, value) => {
    console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
    return Reflect.set(obj, prop, value);
  },
});

personProxy.name;
personProxy.age = 43;
personProxy.name = 'Jane Doe';
```

---

프록시는 객체의 동작에 대한 제어를 추가하는 강력한 방법입니다. 프록시는 다양한 사용 사례를 가질 수 있습니다: 유효성 검사, 포매팅, 알림 또는 디버깅에 도움이 될 수 있습니다.

`Proxy` 객체를 과도하게 사용하거나 `handler` 메소드를 호출할 때마다 무거운 연산을 수행하면 애플리케이션에 쉽게 부정적인 영향을 줄 수 있습니다. 성능이 중요한 코드에는 프록시를 사용하지 않는 것이 가장 좋습니다.
