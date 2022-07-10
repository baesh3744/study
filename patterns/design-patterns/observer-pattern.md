# Observer Pattern

> Observable을 사용하여 이벤트가 발생하면 구독자에게 알립니다.

**Observer pattern**을 사용하면 **observer**라고 불리는 특정 객체가 **observable**이라고 불리는 다른 객체를 구독할 수 있습니다. 이벤트가 발생할 때마다 observable은 모든 observer에게 알립니다!

---

observable 객체는 일반적으로 3가지 중요한 부분을 포함합니다:

- `observers`: 특정 이벤트가 발생할 때마다 알림을 받을 observer 배열
- `subscribe()`: Observer 배열에 observer를 추가하기 위한 메소드
- `unsubscribe()`: Observer 배열에서 observer를 제거하기 위한 메소드
- `notify()`: 특정 이벤트가 발생할 때마다 모든 observer에게 알리기 위한 메소드

---

Observable을 생성해 보겠습니다! ES6 클래스를 사용하면 쉽게 생성할 수 있습니다.

```js
class Observable {
  constructor() {
    this.observers = [];
  }

  subscribe(func) {
    this.observers.push(func);
  }

  unsubscribe(func) {
    this.observers = this.observers.filter((observer) => observer !== func);
  }

  notify(data) {
    this.observers.forEach((observer) => observer(data));
  }
}
```

이제 subscribe 메소드를 사용하여 observer 배열에 observer를 추가하고, unsubscribe 메소드를 사용하여 observer를 제거하고, notify 메소드를 사용하여 모든 구독에 알릴 수 있습니다.

이 observable로 무언가를 만들어 봅시다. 컴포넌트 2개로만 구성된 매우 기본적인 앱이 있습니다: `Button`과 `Switch`

```jsx
export default function App() {
  return (
    <div className="App">
      <Button>Click me!</Button>
      <FormControlLabel control={<Switch />} />
    </div>
  );
}
```

우리는 애플리케이션과의 **사용자 상호 작용**을 추적하고 싶습니다. 사용자가 버튼을 클릭하거나 스위치를 토글할 때마다 이 이벤트를 타임스탬프와 함께 로깅하려고 합니다. 로깅 외에도 이벤트가 발생할 때마다 표시되는 토스트 알림을 만들고 싶습니다!

사용자가 `handleClick`이나 `handleToggle` 함수를 호출할 때마다 함수는 observer에서 `notify` 메소드를 호출합니다. `notify` 메소드는 `handleClick`이나 `handleToggle` 함수로부터 전달받은 데이터를 모든 구독자에게 알립니다!

먼저, `logger`와 `toastify` 함수를 만들어 보겠습니다. 이 함수들은 결국 `notify` 메소드로부터 `data`를 받게 될 것입니다.

```jsx
import { ToastContainer, toast } from 'react-toastify';

function logger(data) {
  console.log(`${Date.now()} ${data}`);
}

function toastify(data) {
  toast(data);
}

export default function App() {
  return (
    <div className="App">
      <Button>Click me!</Button>
      <FormControlLabel control={<Switch />} />
      <ToastContainer />
    </div>
  );
}
```

현재 `logger`와 `toastify` 함수는 observable을 인식하지 못합니다: Observable은 아직 알릴 수 없습니다! 이 함수들을 observer로 만들려면 observable의 `subscribe` 메소드를 사용하여 이들을 구독해야 합니다!

```jsx
import { ToastContainer, toast } from 'react-toastify';

function logger(data) {
  console.log(`${Date.now()} ${data}`);
}

function toastify(data) {
  toast(data);
}

observable.subscribe(logger);
observable.subscribe(toastify);

export default function App() {
  return (
    <div className="App">
      <Button>Click me!</Button>
      <FormControlLabel control={<Switch />} />
      <ToastContainer />
    </div>
  );
}
```

이벤트가 발생할 때마다 `logger`와 `toastify` 함수는 알림을 받습니다. 이제 실제로 알림을 주는 함수를 구현해야 합니다: `handleClick`과 `handleToggle`! 이 함수들은 observable의 `notify` 메소드를 호출하고 observer가 받아야 하는 데이터를 전달해야 합니다.

```jsx
import { ToastContainer, toast } from 'react-toastify';

function logger(data) {
  console.log(`${Date.now()} ${data}`);
}

function toastify(data) {
  toast(data);
}

observable.subscribe(logger);
observable.subscribe(toastify);

export default function App() {
  function handleClick() {
    observable.notify('User clicked button!');
  }

  function handleToggle() {
    observable.notify('User toggled switch!');
  }

  return (
    <div className="App">
      <Button>Click me!</Button>
      <FormControlLabel control={<Switch />} />
      <ToastContainer />
    </div>
  );
}
```

우리는 방금 전체 플로우를 마쳤습니다: `handleClick`과 `handleToggle`가 observer에서 데이터를 사용하여 `notify` 메소드를 호출한 후, observer가 구독자에게 알립니다: 이 경우에는 `logger`와 `toastify` 함수입니다.

사용자가 컴포넌트 중 하나와 상호 작용할 때마다 `logger`와 `toastify` 함수는 `notify` 메소드에 전달한 데이터를 알림 받습니다!

```jsx
// App.js
import React from 'react';
import { Button, Switch, FormControlLabel } from '@material-ui/core';
import { ToastContainer, toast } from 'react-toastify';
import observable from './Observable';

function handleClick() {
  observable.notify('User clicked button!');
}

function handleToggle() {
  observable.notify('User toggled switch!');
}

function logger(data) {
  console.log(`${Date.now()} ${data}`);
}

function toastify(data) {
  toast(data, {
    position: toast.POSITION.BOTTOM_RIGHT,
    closeButton: false,
    autoClose: 2000,
  });
}

observable.subscribe(logger);
observable.subscribe(toastify);

export default function App() {
  return (
    <div className="App">
      <Button variant="contained" onClick={handleClick}>
        Click me!
      </Button>
      <FormControlLabel control={<Switch name="" onChange={handleToggle} />} label="Toggle me!" />
      <ToastContainer />
    </div>
  );
}
```

```jsx
// Observable.js
class Observable {
  constructor() {
    this.observers = [];
  }

  subscribe(f) {
    this.observers.push(f);
  }

  unsubscribe(f) {
    this.observers = this.observers.filter((subscriber) => subscriber !== f);
  }

  notify(data) {
    this.observers.forEach((observer) => observer(data));
  }
}

export default new Observable();
```

---

Observer pattern을 여러 가지 방법으로 사용할 수 있지만 **비동기, 이벤트 기반 데이터**를 다룰 때 매우 유용할 수 있습니다. 특정 데이터 다운로드가 완료될 때마다 또는 사용자가 메시지 보드에 새 메시지를 보내고 다른 모든 멤버가 알림을 받아야 할 때마다 특정 컴포넌트가 알림을 받기를 원할 수 있습니다.

---

## 사례

Observable pattern을 사용하는 유명한 라이브러리에는 RxJS가 있습니다.

> "ReactiveX는 Observer pattern과 Iterator pattern을 결합하고 함수형 프로그래밍과 컬렉션을 결합하여 이벤트 시퀀스를 관리하는 이상적인 방법에 대한 요구를 충족합니다. - RxJS"

RxJS를 사용하면 observable을 만들고 특정 이벤트를 구독할 수 있습니다! RxJS 문서에 적혀있는 사용자가 문서에서 드래그했는지 여부를 기록하는 예시를 살펴보겠습니다.

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import { fromEvent, merge } from 'rxjs';
import { sample, mapTo } from 'rxjs/operators';

import './styles.css';

merge(
  fromEvent(document, 'mousedown').pipe(mapTo(false)),
  fromEvent(document, 'mouseover').pipe(mapTo(true)),
)
  .pipe(sample(fromEvent(document, 'mouseup')))
  .subscribe((isDragging) => {
    console.log('Were you dragging?', isDragging);
  });

ReactDOM.render(
  <div className="App">Click or drag anywhere and check the console!</div>,
  document.getElementById('root'),
);
```

RxJS에는 observable pattern으로 동작하는 수많은 내장 기능과 예시가 있습니다.

---

## 장점

Observer pattern을 사용하는 것은 관심사 분리와 단일 책임 원칙을 적용하는 좋은 방법입니다. Observer 객체는 observable 객체와 밀접하게 결합되어 있지 않으며 언제든지 (분리) 결합될 수 있습니다. Observer는 단순히 받은 데이터를 처리하면 되지만, observable 객체는 이벤트를 모니터링해야 합니다.

---

## 단점

Observer가 너무 복잡해지면, 모든 구독자에게 알릴 때 성능 문제를 야기할 수 있습니다.
