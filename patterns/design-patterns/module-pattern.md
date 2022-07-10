# Module Pattern

> 코드를 더 작고 재사용 가능한 조각으로 분할

애플리케이션과 코드베이스의 규모가 커짐에 따라 코드를 유지/관리 가능하고 분리된 상태로 유지하는 것이 점점 더 중요해지고 있습니다. Module pattern을 사용하면 코드를 더 작고 재사용 가능한 조각으로 나눌 수 있습니다.

모듈을 사용하면 코드를 더 작고 재사용 가능한 조각으로 나눌 수 있는 것 외에도 파일 내의 특정한 값을 *비공개*로 유지할 수 있습니다. 모듈 내의 선언은 기본적으로 해당 모듈로 범위가 지정(캡슐화)됩니다. 특정 값을 명시적으로 내보내지 않으면, 그 값은 해당 모듈 외부에서는 사용할 수 없습니다. 모듈을 사용하면 값을 전역 스코프에서 사용할 수 없기 때문에 코드베이스의 다른 부분에서 선언된 값과 이름이 충돌하는 위험이 줄어듭니다.

---

### ES2015 모듈

ES2015에서 자바스크립트 내장 모듈이 도입되었습니다. 모듈은 자바스크립트 코드가 포함된 파일로 일반 스크립트와 약간의 동작 차이가 있습니다.

수학 함수를 포함하는 `math.js`라는 모듈의 예를 살펴보겠습니다.

```js
// math.js
function add(x, y) {
  return x + y;
}

function multiply(x) {
  return x * 2;
}

function subtract(x, y) {
  return x - y;
}

function square(x) {
  return x * x;
}
```

```js
// index.js
console.log(add(2, 3));
console.log(multiply(2));
console.log(subtract(2, 3));
console.log(square(2));
```

간단한 수학 논리가 포함된 `math.js` 파일이 있습니다. 파일 안에는 사용자가 전달한 값을 더하고, 곱하고, 빼고, 제곱하는 함수가 있습니다.

그러나 우리는 `math.js` 파일 안에서 이 함수를 사용하는 것이 아니라 `index.js` 파일 안에서 참조하기를 원합니다! 현재 `index.js` 파일에서는 에러가 발생합니다: `index.js` 파일 안에는 `add`, `subtract`, `multiply`, `square`라는 함수가 없습니다. 우리는 `index.js` 파일에서 사용할 수 없는 함수를 참조하려는 것입니다.

`math.js`의 함수를 다른 파일에서 사용 가능하게 하려면 먼저 해당 함수를 _내보내야 합니다_. 모듈에서 코드를 내보내려면 `export` 키워드를 사용해야 합니다. 함수를 내보내는 방법 중 한 가지는 named export를 사용하는 것입니다: 공개하고 싶은 코드 앞에 `export` 키워드를 추가하기만 하면 됩니다. 이 예시에서는 `index.js` 파일에서 네 함수 모두에 접근해야 하므로 모든 함수 앞에 `export` 키워드를 추가합니다.

```js
// math.js
export function add(x, y) {
  return x + y;
}

export function multiply(x) {
  return x * 2;
}

export function subtract(x, y) {
  return x - y;
}

export function square(x) {
  return x * x;
}
```

`add`, `multiply`, `subtract`, `square` 함수를 내보낼 수 있게 만들었습니다! 그러나 모듈에서 값을 내보내는 것만으로는 모든 파일에서 공개적으로 사용할 수 있도록 하기에 충분하지 않습니다. 모듈에서 내보낸 값을 사용하려면 해당 값을 참조하려는 파일에서 명시적으로 가져와야 합니다.

`import` 키워드를 사용하여 `index.js` 파일 위에서 값을 가져와야 합니다. 함수를 가져올 모듈을 자바스크립트에 알리려면 `from`과 모듈까지의 상대 경로를 추가해야 합니다.

```js
// index.js
import { add, multiply, subtract, square } from './math.js';
```

`math.js` 모듈에서 `index.js` 파일로 함수 4개를 가져왔습니다! 이제 함수를 사용할 수 있는지 살펴봅시다!

```js
// index.js
import { add, multiply, subtract, square } from './math.js';

console.log(add(2, 3));
console.log(multiply(2));
console.log(subtract(2, 3));
console.log(square(2));
```

참조 에러가 사라졌습니다. 이제 모듈에서 내보낸 값을 사용할 수 있습니다!

모듈이 가지는 가장 큰 이점은 `export` 키워드를 사용하여 명시적으로 _내보낸_ 값에만 접근할 수 있다는 것입니다. `export` 키워드를 사용하여 명시적으로 내보내지 않은 값은 모듈 안에서만 사용할 수 있습니다.

`math.js` 파일 안에서만 참조할 수 있는 값을 `privateValue`라는 이름으로 생성해 보겠습니다.

```js
// math.js
const privateValue = 'This is a value private to the module!';

export function add(x, y) {
  return x + y;
}

export function multiply(x) {
  return x * 2;
}

export function subtract(x, y) {
  return x - y;
}

export function square(x) {
  return x * x;
}
```

`privateValue` 앞에 `export` 키워드를 추가하지 않은 것에 주목하세요. `privateValue` 변수를 내보내지 않았기 때문에 `math.js` 모듈 밖에서는 이 값에 접근할 수 없습니다!

```js
// index.js
import { add, multiply, subtract, square } from './math.js';

console.log(privateValue);
/* Error: privateValue is not defined */
```

값을 모듈에 private하게 유지하면 실수로 전역 스코프를 오염시킬 위험이 줄어듭니다. 당신의 모듈을 사용하는 개발자가 실수로 private 값과 동일한 이름을 가진 값을 생성해 덮어쓸까봐 걱정할 필요가 없습니다: 모듈은 이름이 충돌하는 것을 방지합니다.

---

때로는 내보낸 이름이 로컬 값과 충돌할 수 있습니다.

```js
// index.js
import { add, multiply, subtract, square } from './math.js';

function add(...args) {
  return args.reduce((acc, cur) => cur + acc);
}
/* Error: add has already been declared */

function multiply(...args) {
  return args.reduce((acc, cur) => cur * acc);
}
/* Error: multiply has already been declared */
```

이 경우 `index.js`에는 `add`와 `multiply`라는 함수가 있습니다. 같은 이름의 값을 가져오면 이름이 충돌합니다: `add`와 `multiply`는 이미 선언되었습니다! 다행히도 `as` 키워드를 사용하여 가져온 값의 _이름을 바꿀 수 있습니다_.

가져온 `add`와 `multiply` 함수의 이름을 `addValues`와 `multiplyValues`로 바꿔보겠습니다.

```js
// index.js
import { add as AddValues, multiply as multiplyValues, subtract, square } from './math.js';

function add(...args) {
  return args.reduce((acc, cur) => cur + acc);
}

function multiply(...args) {
  return args.reduce((acc, cur) => cur * acc);
}

/* From math.js module */
addValues(7, 8);
multiplyValues(8, 9);
subtract(10, 3);
square(3);

/* From index.js file */
add(8, 9, 2, 10);
multiply(8, 9, 2, 10);
```

`exports` 키워드만으로 정의된 named export 외에도 *default export*를 사용할 수도 있습니다. 모듈당 **하나**의 default export를 가질 수 있습니다.

`add` 함수를 default export로 만들고 다른 함수는 named export로 유지해 보겠습니다. 값 앞에 `export default`를 추가하여 디폴트값을 내보낼 수 있습니다.

```js
// math.js
export default function add(x, y) {
  return x + y;
}

export function multiply(x) {
  return x * 2;
}

export function subtract(x, y) {
  return x - y;
}

export function square(x) {
  return x * x;
}
```

Named export와 default export의 차이는 모듈에서 값을 내보내는 방식으로, 값을 가져와야 하는 방식을 효과적으로 바꿉니다.

이전에는 named export에 중괄호를 사용해야 했습니다: `import { module } from 'module'`. Default export를 사용하면 중괄호 없이 값을 가져올 수 있습니다: `import module from 'module'`.

```js
// index.js
import add, { multiply, subtract, square } from './math.js';

add(7, 8);
multiply(8, 9);
subtract(10, 3);
square(3);
```

중괄호 없이 가져온 값은 default export가 가 있는 경우에는 항상 default export의 값입니다.

자바스크립트는 이 값이 항상 디폴트로 내보내는 값이라는 것을 알고 있기 때문에 가져온 디폴트 값에 내보낼 때와 다른 이름을 지정할 수 있습니다. 예를 들어, `add` 함수를 `add`라는 이름 대신 `addValues`라는 이름으로 사용할 수 있습니다.

```js
// index.js
import addValues, { multiply, subtract, square } from './math.js';

addValues(7, 8);
multiply(8, 9);
subtract(10, 3);
square(3);
```

`add`라는 함수를 내보냈지만 자바스크립트는 default export를 가져오고 있다는 것을 알고 있기 때문에 원하는 대로 이름을 지정하여 가져올 수 있습니다.

별표 `*`를 사용하고 갸져올 모듈의 이름을 지정하여 모듈의 모든 export (named export와 default export 모두를 의미) 를 가져올 수 있습니다. 가져온 값은 가져온 모든 값을 포함하는 객체와 같습니다. 모듈 전체를 `math`로 가져오고 싶다고 가정해 봅시다.

```js
// index.js
import * as math from './math.js';
```

가져온 값은 `math` 객체의 프로퍼티입니다.

```js
// index.js
import * as math from './math.js';

math.default(7, 8);
math.multiply(8, 9);
math.subtract(10, 3);
math.square(3);
```

이 경우, 모듈에서 내보낸 모든 값을 가져옵니다. 이러한 방법은 불필요하게 값을 가져올 수 있으므로 주의해야 합니다.

`*`를 사용하면 내보낸 모든 값만 가져옵니다. 모듈의 private 값은 명시적으로 내보내지 않는 한 모듈을 가져오는 파일에서 여전히 사용할 수 없습니다.

---

### React

애플리케이션을 React로 구성할 때, 종종 많은 양의 컴포넌트를 처리해야 합니다. 이러한 모든 컴포넌트를 하나의 파일에 작성하는 대신, 자체적인 파일로 컴포넌트를 분리하여 기본적으로 각 컴포넌트에 대한 모듈을 생성하도록 할 수 있습니다.

_리스트_, _리스트 항목_, _입력 필드_, *버튼*을 포함하는 기본적인 투두 리스트가 있다고 해봅시다.

```jsx
// index.js
import React from 'react';
import { render } from 'react-dom';

import { TodoList } from './components/TodoList';
import './styles.css';

render(
  <div className="App">
    <TodoList />
  </div>,
  document.getElementById('root'),
);
```

```jsx
// TodoList.js
import React, { useState } from 'react';
import { List, ListItem, ListItemText } from '@material-ui/core';

import Input from './Input';
import Button from './Button';

function InputRow({ addTodoItem }) {
  const [input, setInput] = useState('');

  function addTodo() {
    addTodoItem(input);
    setInput('');
  }

  return (
    <form>
      <Input value={input} onChange={(e) => setInput(e.target.value)} />
      <Button onClick={addTodo} color="primary" variant="outlined">
        Add Item
      </Button>
    </form>
  );
}

export function TodoList() {
  const [todos, setTodos] = useState(['Improve JS skills 💪', 'Pet dog 🐶']);

  function addTodoItem(todo) {
    todo.length && setTodos([...todos, todo]);
  }

  function removeTodoItem(i) {
    todos.splice(i, 1);
    setTodos([...todos]);
  }

  return (
    <div className="todo-list">
      <h1>Todo Items</h1>
      <InputRow addTodoItem={addTodoItem} />
      <List>
        {todos.map((todo, i) => (
          <ListItem key={`${todo}-${i}`}>
            <ListItemText>{todo}</ListItemText>
            <Button color="secondary" onClick={() => removeTodoItem(i)}>
              Remove
            </Button>
          </ListItem>
        ))}
      </List>
    </div>
  );
}
```

```jsx
// Input.js
import React from 'react';
import Input from '@material-ui/core/Input';

const style = {
  root: {
    padding: '5px',
    backgroundColor: '#434343',
    color: '#fff',
  },
};

export default function CustomInput(props, { variant = 'standard' }) {
  return <Input style={style.root} {...props} variant={variant} placeholder="Type..." />;
}
```

```jsx
// Button.js
import React from 'react';
import Button from '@material-ui/core/Button';

const style = {
  root: {
    borderRadius: 3,
    border: 0,
    color: 'white',
    margin: '0 20px',
  },
  primary: {
    background: 'linear-gradient(45deg, #FE6B8B 30%, #FF8E53 90%)',
  },
  secondary: {
    background: 'linear-gradient(45deg, #2196f3 30%, #21cbf3 90%)',
  },
};

export default function CustomButton(props) {
  return (
    <Button {...props} style={{ ...style.root, ...style[props.color] }}>
      {props.children}
    </Button>
  );
}
```

컴포넌트를 각각의 파일로 분리했습니다:

- `List` 컴포넌트를 위한 `TodoList.js`
- 커스터마이징한 `Button` 컴포넌트를 위한 `Button.js`
- 커스터마이징한 `Input` 컴포넌트를 위한 `Input.js`

우리는 앱 전체에서 `material-ui` 라이브러리에서 가져온 기본적인 `Button`과 `Input` 컴포넌트를 사용하고 싶지 않습니다. 대신, 파일의 `styles` 객체에서 정의된 커스텀 스타일을 추가하여 커스텀 버전의 컴포넌트를 사용하려고 합니다. 애플리케이션에서 매번 기본적인 `Button`과 `Input` 컴포넌트를 가져와서 커스텀 스타일을 추가하는 대신, 이제 기본적인 `Button`과 `Input` 컴포넌트를 한 번만 가져오고, 스타일을 추가하고, 커스텀 컴포넌트를 내보낼 수 있습니다.

어떻게 `Button.js`와 `Input.js` 모두에 `style`이라는 객체가 있는지에 주목하세요. 이 값은 모듈 범위에서 동작하기 때문에 이름이 충돌할 위험 없이 변수 이름을 재사용할 수 있습니다.

---

### 동적으로 모듈 가져오기

파일의 맨 위에 있는 모듈을 가져올 때 모든 모듈은 파일의 나머지 부분보다 먼저 로드됩니다. 어떤 경우에는 특정 조건에 따라 모듈을 가져와야 합니다. **Dynamic import**를 사용하면 요청 시 모듈을 가져올 수 있습니다.

```js
import('module').then((module) => {
  module.default();
  module.namedExport();
});

// Or with async/await
(async () => {
  const module = await import('module');
  module.default();
  module.namedExport();
})();
```

이전 챕터에서 사용했던 `math.js`를 동적으로 가져와 봅시다.

사용자가 버튼을 클릭했을 때만 모듈이 로드됩니다.

```js
// index.js
const button = document.getElementById('btn');

button.addEventListener('click', () => {
  import('./math.js').then((module) => {
    console.log('Add: ', module.add(1, 2));
    console.log('Multiply: ', module.multiply(3, 2));

    const button = document.getElementById('btn');
    button.innerHTML = 'Check the console';
  });
});

/* Or with async/await */
button.addEventListener('click', async () => {
  const module = await import('./math.js');
  console.log('Add: ', module.add(1, 2));
  console.log('Multiply: ', module.multiply(3, 2));
});
```

모듈을 동적으로 가져옴으로써 페이지 로딩 시간을 줄일 수 있습니다. 사용자가 필요로 할 때 사용자가 실제로 필요로 하는 코드를 **로드**, **파싱**, **컴파일**하기만 하면 됩니다.

`import()` 함수는 요청 시 모듈을 가져올 수 있는 것 외에도 표현식을 받을 수 있습니다. 주어진 값을 기반으로 모듈을 동적으로 로드하기 위해서 템플릿 리터럴을 전달할 수 있습니다.

위의 예에서 사용자가 날짜를 로드하는 버튼을 클릭했을 때만 `date.js` 모듈을 가져옵니다. `date.js` 모듈은 서드 파티 모듈인 `moment`를 가져옵니다. 이 모듈은 `date.js` 모듈이 로드되었을 때만 가져옵니다. 만약 사용자가 날짜를 보여줄 필요가 없다면, 이 서드 파티 라이브러리를 모두 로드하지 않아도 됩니다.

사용자가 이미지를 로드하는 버튼을 클릭하면 각 이미지가 로드됩니다. 이미지는 로컬 `.png` 파일로, 문자열에 전달한 `num` 값에 따라 로드됩니다.

```js
const res = await import(`../assets/dog${num}.png`);
```

이렇게 하면 하드 코딩된 모듈 경로에 의존하지 않습니다. 사용자 입력, 외부 소스에서 받은 데이터, 함수 결과 등을 기반으로 모듈을 가져올 수 있는 방법에 유연성을 더합니다.

---

Module pattern을 사용하면 공개적으로 노출되어서는 안 되는 코드를 캡슐화할 수 있습니다. 이것은 실수로 이름이 충돌하는 것이나 전역 스코프가 오염되는 것을 방지하여 여러 디펜더시나 네임스페이스를 사용하여 작업할 때 위험을 줄여줍니다. 모든 자바스크립트 런타임에서 ES2015 모듈을 사용하려면 [Babel](https://babeljs.io/)과 같은 트랜스파일러가 필요합니다.
