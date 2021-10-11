# Hook

## useState

```javascript
const [state, setState] = useState(initialState);
```

상태 값과 그 값을 갱신하는 함수를 반환합니다.

첫 렌더링을 하는 동안 반환된 상태 값(state)은 첫 번째 인자의 값(initialState)과 같습니다.

setState 함수는 state를 갱신할 때 사용합니다. 새 상태 값을 받아 컴포넌트 리렌더링을 큐에 등록합니다.

다음 렌더링 시에 useState를 통해 반환받은 첫 번째 값은 항상 최신 state가 됩니다.

React는 setState 함수의 identity가 안정적이고 리렌더링 시에도 변경되지 않을 것을 보장합니다. 이것이 useEffect나 useCallback의 의존성 목록에 이 함수를 포함하지 않아도 되는 이유입니다.

클래스 컴포넌트의 setState 메소드와는 다르게, useState는 갱신된 객체를 자동으로 합치지 않습니다. Function updater form을 객체 전개 연산자와 결합함으로써 이 동작을 복제할 수 있습니다.

```javascript
const [state, setState] = useState({});
setState((prevState) => {
    // Object.assign would also work
    return { ...prevState, ...updatedValues };
});
```

다른 방법으로는 useReducer가 있는데, 이는 여러 개의 하윗값을 포함한 state 객체를 관리하는 데에 더 적합합니다.

### Lazy initial state

initialState 인자는 초기 렌더링 시에 사용하는 state입니다. 이후 렌더링 시에는 이 값은 무시됩니다. 초기 state가 고비용 계산의 결과라면, 초기 렌더링 시에만 실행될 함수를 제공할 수 있습니다.

```javascript
const [state, setState] = useState(() => {
    const initialState = someExpensiveComputation(props);
    return initialState;
});
```

### state 갱신 취소

State Hook을 현재 state와 동일한 값으로 갱신하는 경우, React는 자식을 렌더링하거나 side effect를 수행하지 않습니다. (React는 Oject.is 비교 알고리즘을 사용합니다.)

### Reference

https://ko.reactjs.org/docs/hooks-reference.html#usestate

<br>

## useMemo

```javascript
import { useMemo } from "react";

const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

-   useMemo(생성 함수, 의존성 값의 배열)
-   메모이제이션된 값을 반환합니다. 이를 통해 렌더링 시에 매번 발생하는 고비용 계산을 방지합니다.
-   `useMemo`는 의존성이 변경되었을 때에만 메모이제이션된 값을 다시 계산합니다. 배열이 없는 경우, 매 렌더링 때마다 새 값을 계산합니다.
-   `useMemo`로 전달된 함수는 렌더링 중에 실행됩니다. 통상적으로 렌더링 중에는 하지 않는 것을 이 함수 내에서 하면 안됩니다. 예를 들어, side effects는 `useEffect`에서 하는 일이지 `useMemo`에서 하는 일이 아닙니다.

<br>

## useRef

```javascript
import { useRef } from "react";

const refContainer = useRef(initialValue);
```

-   `useRef`는 `.current` 프로퍼티로 변경 가능한 ref 객체를 반환합니다. 반환된 객체는 전달된 인자(`initialValue`)로 초기화되고, 컴포넌트의 전 생애주기를 통해 유지됩니다.

```javascript
import React, { useRef } from "react";

function TextInputWithFocusButton() {
    const inputEl = useRef(null);
    const onButtonClick = () => {
        // `current` points to the mounted text input element
        inputEl.current.focus();
    };
    return (
        <>
            <input ref={inputEl} type='text' />
            <button onClick={onButtonClick}>Focus the input</button>
        </>
    );
}
```

-   일반적으로 자식 컴포넌트에게 명령적으로 접근할 때 사용합니다.
-   `useRef`를 통해 `.current` 프로퍼티를 변형시키더라도 리렌더링은 발생하지 않습니다. 만약 React가 DOM 노드에 ref를 attach/detach할 때 어떤 코드를 실행하고 싶다면 `callback ref`를 사용해야 합니다.
