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
