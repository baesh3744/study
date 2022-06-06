# control

> `Object`

`control` 객체는 React Hook Form에 컴포넌트를 등록하는 메소드를 포함합니다.

## Rules

**Important:** 객체 프로퍼티에 직접적으로 접근하지 마세요. 내부용입니다.

## Examples

```tsx
import React from 'react';
import { useForm, Controller } from 'react-hook-form';
import { TextField } from '@material-ui/core';

type FormInputs = {
  firstName: string;
};

function App() {
  const { control, handleSubmit } = useForm<FormInputs>();
  const onSubmit = (data: FormInputs) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller as={TextField} name="firstName" control={control} defaultValue="" />

      <input type="submit" />
    </form>
  );
}
```
