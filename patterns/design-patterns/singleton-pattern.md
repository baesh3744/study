# Singleton Pattern

> 애플리케이션 전체에서 단일 전역 인스턴스를 공유

싱글톤은 한 번만 인스턴스화할 수 있고 전역에서 접근할 수 있는 클래스입니다. 이 단일 인스턴스는 애플리케이션 전체에서 공유할 수 있으므로 싱글톤은 애플리케이션의 전역 상태를 관리하는데 적합합니다.

먼저, ES2015 클래스를 사용하여 싱글톤이 어떤 모습인지 살펴보겠습니다. 예시를 위해, 다음을 포함하는 `Counter` 클래스를 생성하겠습니다:

- `getInstance` 메소드는 인스턴스의 값을 반환합니다.

- `getCount` 메소드는 `counter` 변수의 현재 값을 반환합니다.

- `increment` 메소드는 `counter`의 값을 1 증가시킵니다.

- `decrement` 메소드는 `counter`의 값을 1 감소시킵니다.

```js
let counter = 0;

class Counter {
  getInstance() {
    return this;
  }

  getCount() {
    return counter;
  }

  increment() {
    return ++counter;
  }

  decrement() {
    return --counter;
  }
}
```

그러나 이 클래스는 싱글톤의 기준을 충족하지 못합니다! 싱글톤은 **한 번만 인스턴스화**할 수 있어야 합니다. 지금은 `Counter` 클래스의 인스턴스를 여러 개 생성할 수 있습니다.

```js
let counter = 0;

class Counter {
  getInstance() {
    return this;
  }

  getCount() {
    return counter;
  }

  increment() {
    return ++counter;
  }

  decrement() {
    return --counter;
  }
}

const counter1 = new Counter();
const counter2 = new Counter();

console.log(counter1.getInstance() === counter2.getInstance()); // false
```

`new` 메소드를 2번 호출하여 `counter1`과 `counter2`를 서로 다른 인스턴스로 설정하였습니다. `counter1`과 `counter2`의 `getInstance` 메소드는 서로 다른 인스턴스에 대한 참조를 반환했습니다: 엄밀히 말하면 둘은 같지 않습니다!

![](https://user-images.githubusercontent.com/22341362/173222445-05a71691-2e5d-4a41-8d0d-6b7540a4f505.png)

`Counter` 클래스의 인스턴스를 **하나만** 생성할 수 있도록 해보겠습니다.

인스턴스를 하나만 생성할 수 있도록 하는 한 가지 방법은 `instance`라는 변수를 만드는 것입니다. `Counter`의 생성자에서 새로운 인스턴스가 생성될 때 `instance`를 인스턴스에 대한 참조로 설정합니다. 우리는 `instance` 변수에 이미 값이 있는지 확인하여 새로운 인스턴스화를 방지할 수 있습니다. 변수에 값이 이미 존재하는 경우, 인스턴스는 이미 존재하므로 새로운 인스턴스화는 발생해서는 안 됩니다: 사용자에게 알리기 위해 에러가 발생해야 합니다.

```js
let instance;
let counter = 0;

class Counter {
  constructor() {
    if (instance) {
      throw new Error('You can only create one instance!');
    }
    instance = this;
  }

  getInstance() {
    return this;
  }

  getCount() {
    return counter;
  }

  increment() {
    return ++counter;
  }

  decrement() {
    return --counter;
  }
}

const counter1 = new Counter();
const counter2 = new Counter();
// Error: You can only create one instance!
```

이제 더 이상 인스턴스를 여러 개 생성할 수 없습니다.

`Counter`의 인스턴스를 `counter.js` 파일에서 내보내보겠습니다. 그러나 그렇게 하기 전에 인스턴스도 동결해야 합니다. `Object.freeze` 메소드는 소비 코드가 싱글톤을 수정할 수 없도록 합니다. 동결된 인스턴스의 프로퍼티는 추가하거나 수정할 수 없으므로 실수로 싱글톤의 값을 덮어쓸 위험이 줄어듭니다.

```js
let instance;
let counter = 0;

class Counter {
  constructor() {
    if (instance) {
      throw new Error('You can only create one instance!');
    }
    instance = this;
  }

  getInstance() {
    return this;
  }

  getCount() {
    return counter;
  }

  increment() {
    return ++counter;
  }

  decrement() {
    return --counter;
  }
}

const singletonCounter = Object.freeze(new Counter());
export default singletonCounter;
```

---

`Counter` 예시를 구현한 애플리케이션을 살펴보겠습니다. 다음과 같은 파일이 있습니다:

- `counter.js` - `Counter` 클래스를 포함하고, `Counter`의 인스턴스를 default export로 내보냅니다.

- `index.js` - `redButton.js`와 `blueButton.js` 모듈을 로드합니다.

- `redButton.js` - `Counter`를 가져오고, **빨간색** 버튼의 이벤트 리스너로 `Counter`의 `increment` 메소드를 추가하고, `getCount` 메소드를 호출하여 `counter`의 현재 값을 기록합니다.

- `blueButton.js` - `Counter`를 가져오고, **파란색** 버튼의 이벤트 리스너로 `Counter`의 `increment` 메소드를 추가하고, `getCount` 메소드를 호출하여 `counter`의 현재 값을 기록합니다.

`blueButton.js`와 `redButton.js` 둘 다 `counter.js`에서 **같은 인스턴스**를 가져옵니다. 이 인스턴스는 두 파일 모두에서 `Counter`로 가져와집니다.

![](https://user-images.githubusercontent.com/22341362/173223868-d226608f-038d-4b0b-927b-d43338084eaa.png)

`redButton.js`나 `blueButton.js`에서 `increment` 메소드를 호출하면, 두 파일 모두에서 `Counter` 인스턴스의 `counter` 프로퍼티의 값을 업데이트합니다. 빨간색 버튼을 누르든지 파란색 버튼을 누르든지 상관없습니다: 모든 인스턴스에서 같은 값을 공유합니다. 이것이 다른 파일에서 메소드를 호출하더라도 카운터가 계속 1씩 증가하는 이유입니다.

---

## 장단점

하나의 인스턴스로 인스턴스화를 제한하면 잠재적으로 많은 메모리 공간을 절약할 수 있습니다. 매번 새 인스턴스에 대한 메모리를 설정하는 대신 애플리케이션 전체에서 참조하는 하나의 인스턴스에 대한 메모리만 설정하면 됩니다. 그러나 실제로 싱글톤은 **안티 패턴**으로 간주되며, JavaScript에서 피할 수 있습니다 (또는.. 피해야 합니다).

Java나 C++과 같은 많은 프로그래밍 언어에서는 JavaScript에서처럼 객체를 직접 생성할 수 없습니다. 이러한 객체 지향 프로그래밍 언어에서는 객체를 생성하는 클래스를 생성해야 합니다. 생성된 객체는 JavaScript 예시의 `instance` 값과 마찬가지로 클래스의 인스턴스 값을 가집니다.

그러나 위 예시에서 보여준 클래스 구현은 실제로 과합니다. JavaScript에서는 객체를 직접 생성할 수 있으므로 일반 객체를 사용하여 동일한 결과를 얻을 수 있습니다. 싱글톤 사용의 몇 가지 단점을 살펴보겠습니다!

#### 일반 객체 사용

이전에 본 것과 동일한 예시를 사용하겠습니다. 그러나 이번에는 `counter`는 다음을 포함하는 간단한 객체입니다:

- `count` 프로퍼티

- `count` 값을 1 증가시키는 `increment` 메소드

- `count` 값을 1 감소시키는 `decrement` 메소드

```js
// counter.js
let count = 0;

const counter = {
  increment() {
    return ++count;
  },
  decrement() {
    return --count;
  },
};

Object.freeze(counter);
export { counter };
```

객체가 참조로 전달되기 때문에 `redButton.js`와 `blueButton.js` 모두 동일한 `counter` 객체에 대한 참조를 가져옵니다. 이들 파일 중 하나에서 `count` 값을 수정하면 두 파일 모두에서 볼 수 있는 `counter`의 값이 수정됩니다.

### 테스트

싱글톤에 의존하는 테스트 코드는 까다로울 수 있습니다. 매번 새로운 인스턴스를 생성할 수 없기 때문에 모든 테스트는 이전 테스트의 전역 인스턴스 수정에 의존합니다. 이 경우 테스트의 순서가 중요하며 작은 수정 한 번으로 이후 진행되는 테스트 전체가 실패할 수 있습니다. 테스트가 끝난 후에는 테스트에서 수정한 사항을 초기화하기 위해 전체 인스턴스를 초기화해야 합니다.

```js
// test.js
import Counter from '../src/counterTest';

test('incrementing 1 time should be 1', () => {
  Counter.increment();
  expect(Counter.getCount()).toBe(1);
});

test('incrementing 3 extra times should be 4', () => {
  Counter.increment();
  Counter.increment();
  Counter.increment();
  expect(Counter.getCount()).toBe(4);
});

test('decrementing 1 time should be 3', () => {
  Counter.decrement();
  expect(Counter.getCount()).toBe(3);
});
```

#### 의존성 은닉

다른 모듈 (`superCounter.js`) 을 가져올 때, 모듈이 싱글톤을 가져오는지 명확하지 않을 수 있습니다. 다른 파일 (`index.js`) 에서 해당 모듈을 가져오고 메소드를 호출했을 수도 있습니다. 이런 식으로 우리는 실수로 싱글톤의 값을 수정합니다. 애플리케이션 전체에서 싱글톤의 여러 인스턴스를 공유 및 수정할 수 있기 때문에 예기치 않은 동작으로 이어질 수 있습니다.

```js
// superCounter.js
import Counter from './counter';

export default class SuperCounter {
  constructor() {
    this.count = 0;
  }

  increment() {
    Counter.increment();
    return (this.count += 100);
  }

  decrement() {
    Counter.decrement();
    return (this.count -= 100);
  }
}
```

### 전역 동작

싱글톤 인스턴스는 전체 앱에서 참조될 수 있어야 합니다. 전역 변수는 본질적으로 동일한 동작을 보여줍니다: 전역 변수는 전역 스코프에서 사용할 수 있으므로 애플리케이션 전체에서 해당 변수에 접근할 수 있습니다.

전역 변수를 가지는 것은 일반적으로 좋지 않은 결정으로 간주됩니다. 전역 스코프의 오염은 실수로 전역 변수 값을 덮어쓰게 되어 예기치 않은 동작이 많이 발생할 수 있습니다.

ES2015에서 전역 변수를 생성하는 것은 상당히 드문 일입니다. 새로운 `let`과 `const` 키워드는 변수를 선언된 블록 스코프에서만 유지하여 개발자가 실수로 전역 스코프를 오염시키는 것을 방지합니다. JavaScript의 새로운 모듈 시스템은 모듈에서 값을 `export`하고, 다른 파일에서 해당 값을 `import`할 수 있으므로, 전역 스코프를 오염시키지 않고 전역에서 접근 가능한 값을 더 쉽게 생성할 수 있습니다.

그러나 싱글톤의 일반적인 사용 사례는 애플리케이션 전체에서 일종의 **전역 상태**를 갖는 것입니다. 코드베이스의 여러 부분이 동일한 변경 가능한 객체에 의존하면 예기치 않은 동작이 발생할 수 있습니다.

일반적으로 코드베이스의 특정 부분은 전역 상태의 값을 수정하는 반면, 다른 부분은 해당 데이터를 사용합니다. 여기서 실행 순서가 중요합니다: (아직) 소비할 데이터가 없을 때, 실수로 데이터를 먼저 소비하는 것을 원하지 않습니다! 전역 상태를 사용할 때 데이터 흐름을 이해하는 것은 애플리케이션이 성장하고 수십 개의 컴포넌트가 서로 의존함에 따라 매우 까다로워질 수 있습니다.

### React에서 상태 관리

React에서는 싱글톤을 사용하는 대신 **Redux**나 **React Context**와 같은 상태 관리 도구를 통해 전역 상태에 의존하는 경우가 많습니다. 이러한 상태 관리 도구의 동작이 싱글톤의 동작과 유사해 보일 수 있지만, 싱글톤의 변경 가능한 상태가 아닌 **읽기 전용 상태**를 제공합니다. Redux를 사용할 때 컴포넌트가 dispatcher를 통해 action을 보내면 순수 함수인 reducer만 상태를 업데이트할 수 있습니다.

이러한 도구를 사용한다고 전역 상태의 단점이 마법처럼 사라지지는 않지만, 컴포넌트가 상태를 직접 업데이트할 수 없기 때문에 최소한 전역 상태가 의도한 대로 변경되도록 할 수 있습니다.
