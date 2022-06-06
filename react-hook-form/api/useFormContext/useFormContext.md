# useFormContext

> `Function`

이 커스텀 훅은 form context에 접근할 수 있게 해줍니다. `useFormContext`는 context를 prop으로 전달하는 것이 불편할 수 있는 깊이 중첩된 구조에서 사용하기 위한 것입니다.

## Props

다음 표는 `FormProvider`에 적용되며, `useFormContext`는 인수를 허용하지 않습니다.

| 이름     | 타입     | 설명                                                      |
| -------- | -------- | --------------------------------------------------------- |
| ...props | `Object` | `FormProvider`는 `useForm`의 모든 메소드를 필요로 합니다. |

## Rules

`useFormContext`가 정상적으로 동작하기 위해서는 form을 `FormProvider`로 감싸야 합니다.

## Examples

```jsx
import React from 'react';
import { useForm, FormProvider, useFormContext } from 'react-hook-form';

export default function App() {
  const methods = useForm();
  const onSubmit = (data) => console.log(data);

  return (
    // 모든 메소드를 context로 전달합니다.
    <FormProvider {...methods}>
      <form onSubmit={methods.handleSubmit(onSubmit)}>
        <NestedInput />
        <input types="submit" />
      </form>
    </FormProvider>
  );
}

function NestedInput() {
  const { register } = useFormContext(); // 모든 hook 메소드를 검색합니다.
  return <input {...register('test')} />;
}
```
