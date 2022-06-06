# ErrorMessage

> `Component`

관련된 입력의 에러 메시지를 렌더링하기 위한 단순한 컴포넌트입니다.

```
npm install @hookform/error-message
```

## Props

| 이름    | 타입                                                                    | 필수 | 설명                                                                                                                                                                                                  |
| ------- | ----------------------------------------------------------------------- | ---- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name    | `string`                                                                | ✔︎   | 필드의 이름입니다.                                                                                                                                                                                    |
| errors  | `object`                                                                |      | React Hook Form의 `errors` 객체입니다. `FormProvider`를 사용하고 있는 경우에는 옵션입니다.                                                                                                            |
| message | `string \| React.ReactElement`                                          |      | 인라인 에러 메시지입니다.                                                                                                                                                                             |
| as      | `React.ElementType \| string`                                           |      | 래퍼 컴포넌트나 HTML 태그입니다. e.g. `as="span"`, `as={<Text />}`                                                                                                                                    |
| render  | `({ message: string \| React.ReactElement, messages?: Object }) => any` |      | 에러 메시지나 메시지들을 렌더링하기 위한 [render prop](https://reactjs.org/docs/render-props.html)입니다.<br><br>**Note:** `messages`를 사용하기 위해서는 `criteriaMode`를 `'all'`로 설정해야 합니다. |

## Examples

```tsx
// Single error message
import React from 'react';
import { useForm } from 'react-hook-form';
import { ErrorMessage } from '@hookform/error-message';

interface FormInputs {
  singleErrorInput: string;
}

export default function App() {
  const {
    register,
    formState: { errors },
    handleSubmit,
  } = useForm<FormInputs>();
  const onSubmit = (data: FormInputs) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('singleErrorInput', { required: 'This is required.' })} />
      <ErrorMessage errors={errors} name="singleErrorInput" />

      <ErrorMessage
        errors={errors}
        name="singleErrorInput"
        render={({ message }) => <p>{message}</p>}
      />

      <input type="submit" />
    </form>
  );
}
```

```tsx
// Multiple error messages
import React from 'react';
import { useForm } from 'react-hook-form';
import { ErrorMessage } from '@hookform/error-message';

interface FormInputs {
  multipleErrorInput: string;
}

export default function App() {
  const {
    register,
    formState: { errors },
    handleSubmit,
  } = useForm<FormInputs>({
    criteriaMode: 'all',
  });
  const onSubmit = (data: FormInputs) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register('multipleErrorInput', {
          required: 'This is required.',
          pattern: {
            value: /d+/,
            message: 'This input is number only.',
          },
          maxLength: {
            value: 10,
            message: 'This input exceed maxLength.',
          },
        })}
      />
      <ErrorMessage
        errors={errors}
        name="multipleErrorInput"
        render={({ messages }) =>
          messages && Object.entries(messages).map(([type, message]) => <p key={type}>{message}</p>)
        }
      />

      <input type="submit" />
    </form>
  );
}
```
