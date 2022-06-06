# resetField

> `(name: string, options?: Record<string, boolean | any>) => void`

개별 필드의 상태를 초기화합니다.

## Props

이 함수를 호출하고나면

- `isValid`가 재평가됩니다.
- `isDirty`가 재평가됩니다.

`resetField`에는 필드 상태를 유지하는 기능이 있습니다. 다음은 사용할 수 있는 옵션입니다.

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
<td colspan=2>name</td>
<td>

`string`

</td>
<td>

등록된 필드의 이름

</td>
</tr>
<tr>
<td rowspan=4>options</td>
<td>keepError</td>
<td>

`boolean`

</td>
<td>

`true`로 설정하면, 필드 에러가 유지됩니다.

</td>
</tr>
<tr>
<td>keepDirty</td>
<td>

`boolean`

</td>
<td>

`true`로 설정하면, `dirtyFields`가 유지됩니다.

</td>
</tr>
<tr>
<td>keepTouched</td>
<td>

`boolean`

</td>
<td>

`true`로 설정하면, `touchedFields`가 유지됩니다.

</td>
</tr>
<tr>
<td>defaultValue</td>
<td>

`unknown`

</td>
<td>

이 값이 제공되지 않으면, 필드는 `defaultValue`로 되돌아 갑니다.

이 값이 제공되면

- 필드는 제공된 값으로 업데이트됩니다.
- 필드의 `defaultValue`가 이 값으로 업데이트됩니다.

</td>
</tr>
</tbody>
</table>

## Rules

- `name`은 등록된 필드의 이름과 일치해야 합니다.

```js
register('test');

resetField('test'); // ✔︎ register과 resetField가 정상적으로 동작합니다.
resetField('non-existent-name'); // ✘ 입력을 찾을 수 없어 실패합니다.
```

## Examples

```jsx
// Reset field state
import React from 'react';
import { useForm } from 'react-hook-form';

export default function App() {
  const {
    register,
    resetField,
    formState: { isDirty, isValid },
  } = useForm({
    mode: 'onChange',
    defaultValues: {
      firstName: '',
    },
  });
  const handleClick = () => resetField('firstName');

  return (
    <form>
      <input {...register('firstName', { required: true })} />

      <p>{isDirty && 'dirty'}</p>
      <p>{isValid && 'valid'}</p>

      <button type="button" onClick={handleClick}>
        Reset
      </button>
    </form>
  );
}
```

```jsx
// Reset with options
import React from 'react';
import { useForm } from 'react-hook-form';

export default function App() {
  const {
    register,
    resetField,
    formState: { isDirty, isValid, errors, touchedFields, dirtyFields },
  } = useForm({
    mode: 'onChange',
    defaultValues: {
      firstName: '',
    },
  });

  return (
    <form>
      <input {...register('firstName', { required: true })} />

      <p>isDirty: {isDirty && 'dirty'}</p>
      <p>touchedFields: {touchedFields.firstName && 'touched fields'}</p>
      <p>dirtyFields: {dirtyFields.firstName && 'dirty field'}</p>
      <p>isValid: {isValid && 'valid'}</p>
      <p>error: {errors.firstName && 'error'}</p>

      <hr />

      <button type="button" onClick={() => resetField('firstName', { keepError: true })}>
        Reset keep error
      </button>
      <button type="button" onClick={() => resetField('firstName', { keepTouched: true })}>
        Reset keep touched fields
      </button>
      <button type="button" onClick={() => resetField('firstName', { keepDirty: true })}>
        Reset keep dirty fields
      </button>
      <button type="button" onClick={() => resetField('firstName', { defaultValue: 'New' })}>
        Update defaultValue
      </button>
    </form>
  );
}
```
