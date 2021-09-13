# Hook

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
