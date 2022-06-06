# setFocus

> `(name: string, options: SetFocusOptions) => void`

이 메소드를 사용하면 사용자가 프로그래밍 방식으로 입력에 focus할 수 있습니다. 입력의 ref가 hook form에 등록되었는지 확인하세요.

## Props

<table>
<thead>
<tr>
<th colspan=2>이름</th>
<th>타입</th>
<th>설명</th>
</tr>
</thead>
<tbody>
<tr>
<th colspan=2>name</th>
<td>

`string`

</td>
<td>

focus할 입력 필드의 이름

</td>
</tr>
<tr>
<td>options</td>
<td>shouldSelect</td>
<td>

`boolean`

</td>
<td>

focus할 때 입력의 내용을 선택할지 여부

```js
const { setFocus } = useForm();

setFocus('name', { shouldSelect: true });
```

</td>
</tr>
</tbody>
</table>

## Rules

이 API는 ref에서 `focus` 메소드를 호출하므로 등록하는 과정에서 ref를 제공하는 것이 중요합니다.

## Examples

```tsx
import React, { useEffect } from 'react';
import { useForm } from 'react-hook-form';

type FormValues = {
  firstName: string;
};

export default function App() {
  const { register, handleSubmit, setFocus } = useForm<FormValues>();
  const onSubmit = (data: FormValues) => console.log(data);

  useEffect(() => {
    setFocus('firstName');
  }, [setFocus]);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName')} placeholder="First Name" />
      <input type="submit" />
    </form>
  );
}
```
