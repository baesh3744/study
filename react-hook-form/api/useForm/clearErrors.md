# clearErrors

> `(name?: string | string[]) => void`

이 함수는 form의 에러를 수동으로 제거합니다.

## Props

<table>
<thead>
<tr>
<th>타입</th>
<th>설명</th>
<th>예시</th>
</tr>
</thead>
<tbody>
<tr>
<td>

`undefined`

</td>
<td>모든 에러를 제거합니다.</td>
<td>

```js
clearErrors();
```

</td>
</tr>
<tr>
<td>

`string`

</td>
<td>단일 에러를 제거합니다.</td>
<td>

```js
clearErrors('yourDetails.firstName');
```

</td>
</tr>
<tr>
<td>

`string[]`

</td>
<td>여러 에러를 제거합니다.</td>
<td>

```js
clearErrors(['yourDetails.lastName']);
```

</td>
</tr>
</tbody>
</table>

- `undefined`: 모든 에러를 제거합니다.

- `string`: 단일 필드 또는 키 이름으로 에러를 초기화합니다.

```js
register('test.firstName', { required: true });
register('test.lastName', { required: true });
clearErrors('test'); // test.firstName과 test.lastName의 에러를 모두 제거합니다.
clearErrors('test.firstName'); // 단일 에러를 제거합니다.
```

- `string[]`: 주어진 필드의 에러를 초기화합니다.

## Rules

- 각 입력에 연결된 유효성 검사 규칙에 영향을 미치지 않습니다.

- 이 메소드는 유효성 검사 규칙이나 `isValid` 상태에 영향을 미치지 않습니다.

## Examples

```tsx
import React from 'react';
import { useForm } from 'react-hook-form';

type FormInputs = {
  firstName: string;
  lastName: string;
  username: string;
};

const App = () => {
  const {
    register,
    formState: { errors },
    handleSubmit,
    clearErrors,
  } = useForm<FormInputs>();

  const onSubmit = (data: FormInputs) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName', { required: true })} />
      <input {...register('lastName', { required: true })} />
      <input {...register('username', { required: true })} />

      <button type="button" onClick={() => clearErrors('firstName')}>
        Clear First Name Errors
      </button>
      <button type="button" onClick={() => clearErrors(['firstName', 'lastName'])}>
        Clear First and Last Name Errors
      </button>
      <button type="button" onClick={() => clearErrors()}>
        Clear All Errors
      </button>
      <input type="submit" />
    </form>
  );
};
```
