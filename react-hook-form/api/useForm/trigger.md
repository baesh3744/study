# trigger

> `(name?: string | string[]) => Promise<boolean>`

수동으로 form이나 입력의 유효성 검사를 트리거합니다. 이 메소드는 종속적인 유효성 검사가 있는 경우에도 유용합니다 (입력의 유효성 검사가 다른 입력 값에 따라 다름).

## Props

<table>
<thead>
<tr>
<th>이름</th>
<th>타입</th>
<th>설명</th>
<th>예시</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan=3>name</td>
<td>

`undefined`

</td>
<td>

모든 필드에서 유효성 검사를 트리거합니다.

</td>
<td>

```js
trigger();
```

</td>
</tr>
<tr>
<td>

`string`

</td>
<td>

`name`으로 지정된 필드 값에 대한 유효성 검사를 트리거합니다.

</td>
<td>

```js
trigger('yourDetails.firstName');
```

</td>
</tr>
<tr>
<td>

`string[]`

</td>
<td>

`name`으로 지정된 필드들에 유효성 검사를 트리거합니다.

</td>
<td>

```js
trigger('[yourDetails.lastName'])
```

</td>
</tr>
<tr>
<td>shouldFocus</td>
<td>

`boolean`

</td>
<td>

에러를 설정하는 동안 입력을 focus해야 합니다. 이 옵션은 입력의 레퍼런스가 등록된 경우에만 동작하고 커스텀 등록에는 동작하지 않습니다.

</td>
<td>

```js
trigger('name', { shouldFocus: true });
```

</td>
</tr>
</tbody>
</table>

## Rules

`string`을 payload로 사용하여 단일 필드 이름을 대상으로 하는 경우에만 렌더링 최적화를 분리합니다. `trigger`에 배열이나 `undefined`가 전달되면 `formState` 전체를 리렌더링합니다.

## Examples

```tsx
import React from 'react';
import { useForm } from 'react-hook-form';

type FomInputs = {
  firstName: string;
  lastName: string;
};

export default function App() {
  const {
    register,
    trigger,
    formState: { errors },
  } = useForm<FormInputs>();

  return (
    <form>
      <input {...register('firstName', { required: true })} />
      <input {...register('lastName', { required: true })} />
      <button
        type="button"
        onClick={() => {
          trigger('lastName');
        }}
      >
        Trigger
      </button>
      <button
        type="button"
        onClick={() => {
          trigger(['firstName', 'lastName']);
        }}
      >
        Trigger Multiple
      </button>
      <button
        type="button"
        onClick={() => {
          trigger();
        }}
      >
        Trigger All
      </button>
    </form>
  );
}
```
