# handleSubmit

> `((data: Object, e?: Event) => void, (errors: Object, e?: Event) => void) => Function`

`handleSubmit` 함수는 유효성 검사가 성공하면 form 데이터를 수신합니다.

## Props

| 이름               | 타입                                  | 설명                                       |
| ------------------ | ------------------------------------- | ------------------------------------------ |
| SubmitHandler      | `(data: Object, e?: Event) => void`   | 유효성 검사가 성공했을 때 호출할 콜백 함수 |
| SubmitErrorHandler | `(errors: Object, e?: Event) => void` | 에러가 발생했을 때 호출할 콜백 함수        |

## Rules

- `handleSubmit`을 사용하여 비동기식으로 form을 쉽게 제출할 수 있습니다.

```jsx
// 원격으로 호출할 수 있습니다.
handleSubmit(onSubmit)();

// 비동기 유효성 검사를 위해 async 함수를 전달할 수 있습니다.
handleSubmit(async (data) => await fetchAPI(data));
```

- `disabled`가 설정된 입력은 form 데이터에서 `undefined`로 나타납니다. 사용자가 값을 업데이트하는 것을 막고 `value`의 값을 유지하고 싶다면, `readOnly`를 사용하거나 `<fieldset>` 전체를 `disabled` 설정해야 합니다.

- 콜백 함수가 동작하는 동안 `handleSubmit` 내부에서 에러를 발생시키면, 에러를 버블링하고 `isSubmitSuccessful`는 `false`로 유지됩니다.

```jsx
const onSubmit = () => {
  throw new Error('Something is wrong');
};

(event) =>
  handleSubmit(onSubmit)(event).catch((error) => {
    // catch error
  });
```

### Examples

```tsx
// Sync
import React from 'react';
import { useForm, SubmitHandler } from 'react-hook-form';

type FormValues = {
  firstName: string;
  lastName: string;
  email: string;
};

export default function App() {
  const { register, handleSubmit } = useForm<FormValues>();
  const onSubmit: SubmitHandler<FormValues> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName')} />
      <input {...register('lastName')} />
      <input type="email" {...register('email')} />

      <input type="submit" />
    </form>
  );
}
```

```jsx
// Async
import React from 'react';
import { useForm } from 'react-hook-form';

const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

function App() {
  const {
    register,
    handleSubmit,
    formState: { errors },
    formState,
  } = useForm();
  const onSubmit = async (data) => {
    await sleep(2000);
    if (data.username === 'bill') {
      alert(JSON.stringify(data));
    } else {
      alert('There is an error');
    }
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label htmlFor="username">User Name</label>
      <input placeholder="Bill" {...register('username')} />

      <input type="submit" />
    </form>
  );
}
```
