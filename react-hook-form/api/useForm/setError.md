# setError

> `(name: string, error: FieldError, { shouldFocus?: boolean }) => void`

이 함수를 사용하면, 하나 이상의 에러를 수동으로 설정할 수 있습니다.

## Props

<table>
<thead>
<tr>
<th>이름</th>
<th>타입</th>
<th>설명</th>
</tr>
</thead>
<tbody>
<tr>
<td>name</td>
<td>

`string`

</td>
<td>

입력의 이름

</td>
</tr>
<tr>
<td>error</td>
<td>

`{ type: string, message?: string, types: MultipleFieldErrors }`

</td>
<td>

타입과 메시지와 함께 에러를 설정합니다.

</td>
</tr>
<tr>
<td>config</td>
<td>

`{ shouldFocus?: boolean }`

</td>
<td>

에러를 설정하는 동안 입력을 focus합니다. 이 설정은 입력의 레퍼런스가 등록된 경우에만 동작하며 커스텀 등록에도 동작하지 않습니다.

</td>
</tr>
</tbody>
</table>

## Rules

- 입력이 `register`에서 설정한 규칙을 통과하면, 이 메소드는 관련된 에러를 지속하지 않습니다.

```js
register('registerInput', { minLength: 4 });
setError('registerInput', { type: 'custom', message: 'custom message' });
// minLength 요구 사항을 통과하는 한 유효성 검사를 통과합니다.
```

- 입력 필드와 연결되지 않은 에러는 `clearErrors`로 지워질 때까지 지속됩니다.

```tsx
setError('notRegisteredInput', { type: 'custom', message: 'custom message' });
// 해당 커스텀 에러를 제거하려면 clearError()를 수동으로 호출해야 합니다.
```

- 비동기 유효성 검사 후 사용자에게 에러 피드백을 제공하려는 경우 `handleSubmit` 메소드에서 유용할 수 있습니다. (e.g. API가 유효성 검사 에러를 반환함)

- 입력이 비활성화되면 `shouldFocus`가 동작하지 않습니다.

## Examples

```tsx
// Single error
import React, { useEffect } from 'react';
import { useForm } from 'react-hook-form';

type FormInputs = {
  username: string;
};

const App = () => {
  const {
    register,
    handleSubmit,
    setError,
    formState: { errors },
  } = useForm<FormInputs>();
  const onSubmit = (data: FormInputs) => {
    console.log(data);
  };

  useEffect(() => {
    setError('username', {
      type: 'manual',
      message: 'Dont Forget Your Username Should Be Cool!',
    });
  }, [setError]);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('username')} />
      {errors.username && <p>{errors.username.message}</p>}

      <input type="submit" />
    </form>
  );
};
```

```tsx
// Multiple errors
import React from 'react';
import { useForm } from 'react-hook-form';

type FormInputs = {
  username: string;
  firstName: string;
};

const App = () => {
  const {
    register,
    handleSubmit,
    setError,
    formState: { errors },
  } = useForm<FormInputs>();
  const onSubmit = (data: FormInputs) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label>Username</label>
      <input {...register('username')} />
      {errors.username && <p>{errors.username.message}</p>}

      <label>First Name</label>
      <input {...register('firstName')} />
      {errors.firstName && <p>{errors.firstName.message}</p>}

      <button
        type="button"
        onClick={() => {
          [
            {
              type: 'manual',
              name: 'username',
              message: 'Double Check This',
            },
            {
              type: 'manual',
              name: 'firstName',
              message: 'Triple Check This',
            },
          ].forEach(({ name, type, message }) => setError(name, { type, message }));
        }}
      >
        Trigger Name Errors
      </button>
      <input type="submit" />
    </form>
  );
};
```

```tsx
// Single field errors
import React, { useEffect } from 'react';
import { useForm } from 'react-hook-form';

type FormInputs = {
  lastName: string;
};

const App = () => {
  const {
    register,
    handleSubmit,
    setError,
    formState: { errors },
  } = useForm<FormInputs>({
    criteriaMode: 'all',
  });
  const onSubmit = (data: FormInputs) => console.log(data);

  useEffect(() => {
    setError('lastName', {
      types: {
        required: 'This is required',
        minLength: 'This is minLength',
      },
    });
  }, [setValue]);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label>Last Name</label>
      <input {...register('lastName')} />
      {errors.lastName && errors.lastName.types && <p>{errors.lastName.types.required}</p>}
      {errors.lastName && errors.lastName.types && <p>{errors.lastName.types.minLength}</p>}
      <input type="submit" />
    </form>
  );
};
```
