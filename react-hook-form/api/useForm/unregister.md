# unregister

> `(name: string | string[], options?) => void`

`unregister`는 한 개 이상의 입력의 등록을 취소합니다. 또한 입력의 등록을 취소한 후 상태를 유지하기 위한 두 번째 매개변수를 제공합니다.

## Props

아래 예시는 `unregister` 메소드를 호출할 때 예상되는 결과를 보여줍니다.

```jsx
<input {...register('yourDetails.firstName')} />
<input {...register('yourDetails.lastName')} />
```

| 타입       | 입력의 이름                            | 값                 |
| ---------- | -------------------------------------- | ------------------ |
| `string`   | `unregister('yourDetails')`            | `{}`               |
| `string`   | `unregister('youDetails.firstName')`   | `{ lastName: '' }` |
| `string[]` | `unregister(['yourDetails.lastName'])` | `''`               |

## Options

<table>
<thead>
<tr>
<th>이름</th>
<th>타입</th>
<th>설명</th>
<th>코드 예시</th>
</tr>
</thead>
<tbody>
<tr>
<td>keepDirty</td>
<td>

`boolean`

</td>
<td>

`isDirty`와 `dirtyFields` 값을 유지합니다. 단, `isDirty`는 `defaultValues`와의 비교로 값이 결정되기 때문에, 다음 입력에서 `isDirty` 상태가 변화하지 않는다는 보장은 할 수 없습니다.

</td>
<td>

```js
unregister('test', {
  keepDirty: true,
});
```

</td>
</tr>
<tr>
<td>keepTouched</td>
<td>

`boolean`

</td>
<td>

입력의 등록이 취소되어도, `touchedFields`는 제거되지 않습니다.

</td>
<td>

```js
unregister('test', {
  keepTouched: true,
});
```

</td>
</tr>
<tr>
<td>keepValid</td>
<td>

`boolean`

</td>
<td>

`isValid` 값을 유지합니다. 단, 다음 입력이 스키마 유효성 검사를 위해 `isValid`를 업데이트하지 않는다는 보장은 없습니다. 등록 취소에 따라 스키마를 조정해야 합니다.

</td>
<td>

```js
unregister('test', {
  keepValid: true,
});
```

</td>
</tr>
<tr>
<td>keepError</td>
<td>

`boolean`

</td>
<td>

`errors`가 업데이트되지 않습니다.

</td>
<td>

```js
unregister('test', {
  keepError: true,
});
```

</td>
</tr>
<tr>
<td>keepValue</td>
<td>

`boolean`

</td>
<td>

입력의 최근 값이 업데이트되지 않습니다.

</td>
<td>

```js
unregister('test', {
  keepValue: true,
});
```

</td>
</tr>
<tr>
<td>keepDefaultValue</td>
<td>

`boolean`

</td>
<td>

`useForm`에서 정의된 입력의 `defaultValue`가 유지됩니다.

</td>
<td>

```js
unregister('test', {
  keepDefaultValue: true,
});
```

</td>
</tr>
</tbody>
</table>

## Rules

- 이 메소드는 입력의 레퍼런스와 값을 제거합니다. 즉, 내장된 유효성 검사 규칙도 제거됩니다.

- 입력을 등록 취소해도 스키마 유효성 검사에는 영향을 주지 않습니다.

```jsx
const schema = yup
  .object()
  .shape({
    firstName: yup.string().required(),
  })
  .required();

unregister('firstName'); // firstName 입력에 대한 유효성 검사를 제거하지 않습니다.
```

- `register` 콜백이 있는 입력을 언마운트했는지 확인하세요. 그렇지 않으면 입력이 다시 등록됩니다.

```jsx
const [show, setShow] = useState(true);

const onClick = () => {
  unregister('test');
  setShow(false); // register가 다시 호출되지 않도록 해당 입력을 언마운트해야 합니다.
};

{
  show && <input {...register('test')} />;
}
```

## Examples

```tsx
import React, { useEffect } from 'react';
import { useForm } from 'react-hook-form';

interface IFormInputs {
  firstName: string;
  lastName?: string;
}

export default function App() {
  const { register, handleSubmit, unregister } = useForm<IFormInputs>();
  const onSubmit = (data: IFormInputs) => console.log(data);

  useEffect(() => {
    register('lastName');
  }, [register]);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <button type="button" onClick={() => unregister('lastName')}>
        unregister
      </button>
      <input types="submit" />
    </form>
  );
}
```
