# Provider Pattern

> 여러 자식 컴포넌트에서 데이터를 사용할 수 있도록 설정

우리는 경우에 따라서는 애플리케이션의 (전부는 아니지만) 많은 컴포넌트에서 데이터를 사용할 수 있도록 하려고 합니다. `props`를 사용하여 컴포넌트에 데이터를 전달할 수도 있지만, 애플리케이션의 거의 모든 컴포넌트가 props 값에 접근해야 하는 경우에는 이 방법은 어려울 수 있습니다.

우리는 결국 props를 컴포넌트 트리 아래로 전달하는 prop drilling을 하게 됩니다. props에 의존하는 코드를 리팩토링하는 것은 거의 불가능해지고, 특정 데이터의 출처를 아는 것은 어렵습니다.

특정 데이터를 포함하는 `App` 컴포넌트가 있다고 가정해 보겠습니다. 컴포넌트 트리 아래에는 이 데이터를 필요로 하는 `ListItem`, `Header`, `Text` 컴포넌트가 있습니다. 이 데이터를 이러한 컴포넌트로 가져오려면 여러 컴포넌트 계층을 통해 전달해야 합니다.

![](https://user-images.githubusercontent.com/22341362/174011687-7b7fb4b6-4840-4d7c-b05d-859616ea6d2b.png)

코드베이스에서는 다음과 같이 보일 것입니다:

```jsx
function App() {
  const data = { ... };

  return (
    <div>
      <SideBar data={data} />
      <Content data={data} />
    </div>
  );
}

const SideBar = ({ data }) => <List data={data} />;
const List = ({ data }) => <ListItem data={data} />;
const ListItem = ({ data }) => <span>{data.listItem}</span>;

const Content = ({ data }) => (
  <div>
    <Header data={data} />
    <Block data={data} />
  </div>
);
const Header = ({ data }) => <div>{data.title}</div>;
const Block = ({ data }) => <Text data={data} />;
const Text = ({ data }) => <h1>{data.text}</h1>;
```

이런 식으로 props를 전달하는 것은 코드가 많이 지저분해질 수 있습니다. 나중에 `data` prop의 이름을 바꾸려면, 모든 컴포넌트에서 prop의 이름을 바꿔야 합니다. 애플리케이션의 규모가 커질수록 prop drilling이 더 까다로워질 수 있습니다.

이 데이터를 사용할 필요가 없는 컴포넌트를 건너뛸 수 있다면 최적일 것입니다. 우리는 `data`의 값에 직접 접근해야 하는 컴포넌트에 prop drilling에 의존하지 않고 값에 접근할 수 있는 기능을 제공해야 합니다.

이것이 **Provider Pattern**이 유용한 점입니다! Provider Pattern을 사용하면 여러 컴포넌트에서 데이터를 사용할 수 있습니다. props를 통해 데이터를 각 계층 아래로 전달하는 대신, 모든 컴포넌트를 `Provider`로 래핑할 수 있습니다. Provider는 `Context` 객체가 제공하는 고차 컴포넌트입니다. React가 제공하는 `createContext` 메소드를 사용해서 Context 객체를 생성할 수 있습니다.

Provider는 전달하려는 데이터를 포함하는 `value` prop을 받습니다. 이 provider로 래핑된 모든 컴포넌트는 `value` prop의 값에 접근할 수 있습니다.

```jsx
const DataContext = React.createContext();

function App() {
  const data = { ... };

  return (
    <div>
      <DataContext.Provider value={data}>
        <SideBar />
        <Content />
      </DataContext.Provider>
    </div>
  );
}
```

더 이상 `data` prop을 각 컴포넌트에 수동으로 전달할 필요가 없습니다! 그렇다면 `ListItem`, `Header`, `Text` 컴포넌트는 어떻게 `data`의 값에 접근할 수 있을까요?

각 컴포넌트는 `useContext` 훅을 사용해서 `data`에 접근할 수 있습니다. 이 훅은 `data`가 참조하는 컨텍스트 (이 경우에는 `DataContext`) 를 받습니다. `useContext` 훅을 사용하면 컨텍스트 객체에 데이터를 읽고 쓸 수 있습니다.

```jsx
const DataContext = React.createContext();

function App() {
  const data = {};

  return (
    <div>
      <DataContext.Provider value={data}>
        <SideBar />
        <Content />
      </DataContext.Provider>
    </div>
  );
}

const SideBar = () => <List />;
const List = () => <ListItem />;
const Content = () => (
  <div>
    <Header />
    <Block />
  </div>
);
const Block = () => <Text />;

function ListItem() {
  const { data } = React.useContext(DataContext);
  return <span>{data.listItem}</span>;
}

function Header() {
  const { data } = React.useContext(DataContext);
  return <div>{data.title}</div>;
}

function Text() {
  const { data } = React.useContext(DataContext);
  return <h1>{data.text}</h1>;
}
```

`data` 값을 사용하지 않는 컴포넌트는 `data`를 다룰 필요가 없습니다. 더 이상 props 값이 필요하지 않은 컴포넌트를 통해 props를 여러 레벨로 전달하는 것을 걱정할 필요가 없으므로 리팩토링이 훨씬 쉬워집니다.

![](https://user-images.githubusercontent.com/22341362/174045464-3ec449f0-dcbb-4856-a67c-ca2014a2a091.png)

---

Provider Pattern은 전역 데이터를 공유하는데 유용합니다. 일반적인 사용 사례는 많은 컴포넌트와 테마 UI 상태를 공유하는 것입니다.

우리는 사용자가 스위치를 토글하여 라이트 모드와 다크 모드 사이를 전환할 수 있기를 원합니다. 사용자가 다크 모드에서 라이트 모드로 또는 그 반대로 전환하면 배경색과 글자색이 변경되어야 합니다! 현재 테마 값을 각 컴포넌트에 전달하는 대신, 컴포넌트를 `ThemeProvider`로 래핑하고 현재 테마 색상을 provider에 전달할 수 있습니다.

```jsx
export const ThemeContext = React.createContext();

const themes = {
  light: {
    background: '#fff',
    color: '#000',
  },
  dark: {
    background: '#171717',
    color: '#fff',
  },
};

export default function App() {
  const [theme, setTheme] = useState('dark');

  function toggleTheme() {
    setTheme(theme === 'light' ? 'dark' : 'light');
  }

  const providerValue = {
    theme: themes[theme],
    toggleTheme,
  };

  return (
    <div className={`App theme-${theme}`}>
      <ThemeContext.Provider value={providerValue}>
        <Toggle />
        <List />
      </ThemeContext.Provider>
    </div>
  );
}
```

`Toggle` 및 `List` 컴포넌트 모두 `ThemeContext` provider로 감싸져 있기 때문에, provider에 `value`로 전달되는 `theme`과 `toggleTheme` 값에 접근할 수 있습니다.

`Toggle` 컴포넌트 안에서 `toggleTheme` 함수를 사용하여 테마를 업데이트할 수 있습니다.

```jsx
import React, { useContext } from 'react';
import { ThemeContext } from './App';

export default function Toggle() {
  const theme = useContext(ThemeContext);

  return (
    <label className="switch">
      <input type="checkbox" onClick={theme.toggleTheme} />
      <span className="slider round" />
    </label>
  );
}
```

`List` 컴포넌트 자체는 테마의 현재 값에 관심이 없습니다. 그러나 `ListItem` 컴포넌트는 다릅니다! `ListItem` 컴포넌트 안에서 `theme` 컨텍스트를 바로 사용할 수 있습니다.

```jsx
import React, { useContext } from 'react';
import { ThemeContext } from './App';

export default function TextBox() {
  const theme = useContext(ThemeContext);

  return <li style={theme.theme}>...</li>;
}
```

우리틑 테마의 현재 값에 관심이 없는 컴포넌트에 데이터를 전달할 필요가 없습니다.

---

## 훅

우리는 컴포넌트에 컨텍스트를 제공하는 훅을 만들 수 있습니다. 각 컴포넌트에서 `useContext`와 Context를 가져오는 대신 필요한 컨텍스트를 반환하는 훅을 사용할 수 있습니다.

```jsx
function useThemeContext() {
  const theme = useContext(ThemeContext);
  return theme;
}
```

유효한 테마인지 확인하기 위해 `useContext(ThemeContext)`가 거짓 값을 반환하는 경우 에러를 발생시키겠습니다.

```jsx
function useThemeContext() {
  const theme = useContext(ThemeContext);
  if (!theme) {
    throw new Error('useThemeContext must be used within ThemeProvider');
  }
  return theme;
}
```

컴포넌트를 `ThemeContext.Provider` 컴포넌트로 바로 감싸는 대신 컴포넌트를 감싸서 값을 제공하는 HOC를 만들 수 있습니다. 이러한 방법으로 컨텍스트 로직을 컴포넌트를 렌더링하는 코드에서 분리하여 provider의 재사용성을 향상시킬 수 있습니다.

```jsx
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('dark');

  function toggleTheme() {
    setTheme(theme === 'light' ? 'dark' : 'light');
  }

  const providerValue = {
    theme: themes[theme],
    toggleTheme,
  };

  return <ThemeContext.Provider value={providerValue}>{children}</ThemeContext.Provider>;
}

export default function App() {
  return (
    <div className={`App theme-${theme}`}>
      <ThemeProvider>
        <Toggle />
        <List />
      </ThemeProvider>
    </div>
  );
}
```

`ThemeContext`에 접근해야 했던 컴포넌트는 이제 간단히 `useThemeContext` 훅을 사용할 수 있습니다.

```jsx
export default function TextBox() {
  const theme = useThemeContext();

  return <li style={theme.theme}>...</li>;
}
```

서로 다른 컨텍스트를 위한 훅을 여러 개 생성하면 데이터를 렌더링하는 컴포넌트에서 provider의 로직을 쉽게 분리할 수 있습니다.

---

## 사례

일부 라이브러리는 내장 provider를 제공하며, provider의 값은 소비 컴포넌트에서 사용할 수 있습니다. 이에 대한 좋은 예는 [styled-components](https://styled-components.com/docs/advanced)입니다.

> "이 예를 이해하는데 styled-components를 사용해본 경험은 필요하지 않습니다."

styled-components 라이브러리는 `ThemeProvider`를 제공합니다. 스타일이 지정된 컴포넌트는 이 provider의 값에 접근할 수 있습니다! Context API를 직접 만드는 대신 사용자에게 제공되는 API를 사용할 수 있습니다!

동일한 List 예시에서 `styled-component` 라이브러리에서 가져온 `ThemeProvider`로 컴포넌트를 감싸 보겠습니다.

```jsx
import { ThemeProvider } from 'styled-components';

export default function App() {
  const [theme, setTheme] = useState('dark');

  function toggleTheme() {
    setTheme(theme === 'light' ? 'dark' : 'light');
  }

  return (
    <div className={`App theme-${theme}`}>
      <ThemeProvider theme={themes[theme]}>
        <>
          <Toggle toggleTheme={toggleTheme} />
          <List />
        </>
      </ThemeProvider>
    </div>
  );
}
```

`ListItem` 컴포넌트에 `style` prop을 전달하는 대신 `styled.li` 컴포넌트로 만들 것입니다. 스타일이 지정된 컴포넌트이므로 `theme` 값에 접근할 수 있습니다!

```jsx
import styled from 'styled-components';

export default function ListItem() {
  return (
    <Li>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut
      labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco
      laboris nisi ut aliquip ex ea commodo consequat.
    </Li>
  );
}

const Li = styled.li`
  ${({ theme }) => `
    background-color: ${theme.backgroundColor};
    color: ${theme.color};
  `}
`;
```

이제 `ThemeProvider`를 사용하여 모든 스타일이 지정된 컴포넌트에 간단하게 스타일을 적용할 수 있습니다.

---

### 장점

Provider pattern / Context API를 사용하면 각 컴포넌트 계층을 통해 수동으로 데이터를 전달할 필요없이 많은 컴포넌트에 데이터를 전달할 수 있습니다.

이들은 코드를 리팩토링할 때 실수로 버그가 발생할 위험을 줄여줍니다. 이전에는 prop의 이름을 바꾸려면 애플리케이션에서 이 값이 사용된 모든 곳에서 prop의 이름을 바꿔야 했습니다.

더 이상 안티 패턴으로 볼 수 있는 prop-drilling을 처리할 필요가 없습니다. 이전에는 특정 prop 값의 출처가 항상 명확한 것은 아니었기 때문에 애플리케이션의 데이터 흐름을 이해하기 어려울 수 있었습니다. Provider pattern을 사용하면 데이터를 다루지 않는 컴포넌트에는 props를 불필요하게 전달할 필요가 없습니다.

Provider pattern을 사용하면 컴포넌트가 전역 상태에 접근할 수 있기 때문에 일종의 전역 상태를 유지하는 것이 용이해집니다.

---

### 단점

Provider pattern을 과도하게 사용하면 경우에 따라서는 성능 문제가 발생할 수 있습니다. 상태가 변할 때마다 컨텍스트를 사용하는 모든 컴폰넌트가 리렌더링됩니다.

예를 들어 보겠습니다. `Button` 컴포넌트의 `Increment` 버튼을 클릭할 때마다 값이 증가하는 간단한 카운터가 있다고 해봅시다. 또한 `Reset` 컴포넌트에는 카운트를 `0`으로 초기화하는 `Reset` 버튼이 있습니다.

그러나 `Increment`를 클릭하면 리렌더링되는 것은 카운트만이 아님을 알 수 있습니다. `Reset` 컴포넌트의 닐찌도 리렌더링됩니다!

```jsx
import React, { useState, createContext, useContext } from 'react';
import ReactDOM from 'react-dom';
import moment from 'moment';

const CountContext = createContext(null);

function Reset() {
  const { setCount } = useCountContext();

  return (
    <div className="app-col">
      <button onClick={() => setCount(0)}>Reset count</button>
      <div>Last reset: {moment().format('h:mm:ss a')}</div>
    </div>
  );
}

function Button() {
  const { count, setCount } = useCountContext();

  return (
    <div className="app-col">
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <div>Current count: {count}</div>
    </div>
  );
}

function useCountContext() {
  const context = useContext(CountContext);
  if (!context) {
    throw new Error('useCountContext has to be used with CountContextProvider');
  }
  return context;
}

function CountContextProvider({ children }) {
  const [count, setCount] = useState(0);
  return <CountContext.Provider value={{ count, setCount }}>{children}</CountContext.Provider>;
}

function App() {
  return (
    <div className="App">
      <CountContextProvider>
        <Button />
        <Reset />
      </CountContextProvider>
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById('root'));
```

`Reset` 컴포넌트도 `useCountContext`를 사용하기 때문에 리렌더링되었습니다. 규모가 작은 애플리케이션에서 이것은 그다지 중요하지 않습니다. 더 큰 애플리케이션에서 자주 업데이트되는 값을 많은 컴포넌트에 전달하면 성능에 부정적인 영향을 줄 수 있습니다.

컴포넌트가 업데이트될 수 있는 불필요한 값을 포함하는 provider를 사용하지 않도록 각 유스케이스별로 여러 provider를 생성할 수 있습니다.
