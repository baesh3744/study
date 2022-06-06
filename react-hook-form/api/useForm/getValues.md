# getValues

> `(payload?: string | string[]) => Object`

form 값을 읽기에 최적화된 헬퍼 메소드입니다. `watch`와 `getValues`의 차이는 `getValues`는 리렌더링을 트리거하거나 입력의 변경 사항을 구독하지 않는다는 것입니다.

## Props

| 타입        | 설명                                          |
| ----------- | --------------------------------------------- |
| `undefined` | form 값 전체를 반환합니다.                    |
| `string`    | form 값의 경로에 있는 값을 가져옵니다.        |
| `array`     | form 값의 경로에 있는 값의 배열을 반환합니다. |

### Examples

아래 예시는 `getValues` 메소드를 호출할 때 예상되는 결과를 보여줍니다.

```jsx
<input {...register('root.test1')} />
<input {...register('root.test2')} />
```

<table>
<thead>
<tr>
<th>호출</th>
<th>결과</th>
</tr>
</thead>
<tbody>
<tr>
<td>

```js
getValues();
```

</td>
<td>

`{ root: { test1: '', test2: '' }}`

</td>
</tr>
<tr>
<td>

```js
getValues('yourDetails');
```

</td>
<td>

`{ test1: '', test2: '' }`

</td>
</tr>
<tr>
<td>

```js
getValues('yourDetails.firstName');
```

</td>
<td>

`{ test1: '' }`

</td>
</tr>
<tr>
<td>

```js
getValues(['yourDetails.lastName']);
```

</td>
<td>

`['']`

</td>
</tr>
</tbody>
</table>

## Rules

- 비활성화된 입력은 `undefined`를 반환합니다. 사용자가 입력을 업데이트하는 것을 막고 필드 값을 유지하려면, `readOnly`를 사용하거나 `<fieldset>`을 비활성화하면 됩니다.

- 첫 렌더링 전에는 `useForm`의 `defaultValues`를 반환합니다.

## Examples

```tsx
import React from 'react';
import { useForm } from 'react-hook-form';

type FormInputs = {
  test: string;
  test1: string;
};

export default function App() {
  const { register, getValues } = useForm<FormInputs>();

  return (
    <form>
      <input {...register('test')} />
      <input {...register('test1')} />

      <button
        type="button"
        onClick={() => {
          const values = getValues; // { test: 'test-input', test1: 'test1-input' }
          const singleValue = getValues('test'); // 'test-input'
          const multipleValues = getValues(['test', 'test1']); // ['test-input', 'test1-input']
        }}
      >
        Get Values
      </button>
    </form>
  );
}
```
