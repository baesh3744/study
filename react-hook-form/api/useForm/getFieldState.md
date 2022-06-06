# getFieldState

> `object`

이 메소드는 개별 필드 상태를 반환하기 위해 react-hook-form v7.25.0부터 도입되었습니다. Typesafe 방식으로 중첩 필드 상태를 검색하려는 경우에 유용합니다.

## Props

| 이름      | 타입     | 설명                                      |
| --------- | -------- | ----------------------------------------- |
| name      | `string` | 등록된 필드 이름                          |
| formState | `object` | `true`로 설정하면 필드 에러가 유지됩니다. |

## Return

| 이름      | 타입                      | 설명                                                                                          |
| --------- | ------------------------- | --------------------------------------------------------------------------------------------- |
| isDirty   | `boolean`                 | 필드가 수정됩니다.<br><br>**Condition:** `dirtyFields`를 구독합니다.                          |
| isTouched | `boolean`                 | 필드가 focus 및 blur 이벤트를 받았습니다.<br><br>**Condition:** `touchedFields`를 구독합니다. |
| invalid   | `boolean`                 | 필드가 유효하지 않습니다.<br><br>**Condition:** `errors`를 구독합니다.                        |
| error     | `undefined \| FieldError` | 필드 에러 객체<br><br>**Condition:** `errors`를 구독합니다.                                   |

## Rules

- `name`은 등록된 필드의 이름과 일치해야 합니다.

```js
register('test');

getFieldState('test'); // ✔︎ 입력을 등록하고 필드 상태를 반환합니다.
getFieldState('non-existent-name'); // ✘ 상태를 false로 반환하고 에러를 undefined로 반환합니다.
```

- `formState`를 구독해야 합니다. 2가지 방법으로 `formState`를 구독할 수 있습니다.

  - `useForm` (전역 레벨에서) 또는 `useFormState` (훅 레벨에서) 와 같은 관련된 훅이 반환한 값을 구조화할 때 필요한 상태 정보를 간단히 정의할 수 있습니다.

    상태 정보를 `getFieldState`로 전달할 필요가 없습니다.

    ```js
    const {
      register,
      formState: { isDirty },
    } = useForm();
    register('test');
    getFieldState('test'); // ✔︎ 입력을 등록하고 필드 상태를 반환합니다.

    // ------------------------------

    // 이것은 useFormState에서도 유효합니다.
    const { isDirty } = useFormState();

    register('test');
    getFieldState('test'); // ✔︎ 입력을 등록하고 필드 상태를 반환합니다.
    ```

  - `useForm()`, `useFormContext()`, `useFormState()`의 `formState`를 `getFieldState()`로 직접 전달할 수 있습니다.

    이 방법이 사용하지 않는 선언에 대해 더 엄격한 lint 또는 컴파일 규칙이 있는 TypeScript 사용자에게 더 선호됩니다.

    ```js
    const { register } = useForm();
    register('test');
    getFieldState('test'); // ✘ formState가 구독되지 않았기 때문에 상태를 업데이트하기 위해 리렌더링할 수 없습니다.

    // ------------------------------

    const { register, formState } = useForm();
    getFieldState('test', formState); // ✔︎ 입력을 등록하고 필드 상태를 반환합니다.
    ```

## Examples

```jsx
import React from 'react';
import { useForm } from 'react-hook-form';

export default function App() {
  const {
    register,
    getFieldState,
    formState: { isDirty, isValid },
  } = useForm({
    mode: 'onChange',
    defaultValues: {
      firstName: '',
    },
  });

  // 렌더링 전이나 렌더링 함수 안에서 호출할 수 있습니다.
  const fieldState = getFieldState('firstName');

  return (
    <form>
      <input {...register('firstName', { required: true })} />
      <p>{getFieldState('firstName').isDirty && 'dirty'}</p>
      <p>{getFieldState('firstName').isTouched && 'touched'}</p>
      <button type="button" onclick={() => console.log(getFieldState('firstName'))}>
        field state
      </button>
    </form>
  );
}
```
