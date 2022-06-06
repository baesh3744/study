# setValue

> `(name: string, value: unknown, config?: Object) => void`

이 함수는 등록된 필드의 값을 동적으로 설정하고 form 상태를 확인하고 업데이트하는 옵션을 가질 수 있습니다. 동시에 불필요한 리렌더링을 피하려고 합니다.

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
<td colspan=2>name</td>
<td>

`string`

</td>
<td>

- `name`을 통해 단일 필드를 대상으로 지정합니다.

- 필드 배열과 함께 사용할 때

  - `replace`나 `update`와 같은 필드 배열의 메소드를 사용하는 것을 권장합니다.
  - 존재하지 않는 필드를 대상으로 할 때 새 필드를 생성하지 않습니다.

  ```js
  const { replace } = useFieldArray({ name: 'test' });
  setValue('test.101.data'); // ✘ 새 필드를 생성하지 않습니다.
  replace([{ data: 'test' }]); // ✔︎ 전체 필드 배열을 새로 고침합니다.
  ```

</td>
</tr>
<tr>
<td colspan=2>value</td>
<td>

`unknown`

</td>
<td>

필드 값입니다. 이 인수는 필수이며 `undefined`는 불가능합니다.

</td>
</tr>
<tr>
<td rowspan=3>options</td>
<td>shouldValidate</td>
<td>

`boolean`

</td>
<td>

- 입력이 유효한지를 계산할지 여부 (`errors` 구독)
- form이 유효한지를 계산할지 여부 (`isValid` 구독)
- 이 옵션은 form의 터치된 필드가 아닌 지정된 필드 수준에서 터치된 필드를 업데이트합니다.

```js
setValue('name', 'value', { shouldValidate: true });
```

</td>
</tr>
<tr>
<td>shouldDirty</td>
<td>

`boolean`

</td>
<td>

- `defaultValues`에 대해 입력의 오염 상태를 계산할지 여부 (`dirtyFields` 구독)
- `defaultValues`에 대해 form의 오염 상태를 계산할지 여부 (`isDirty` 구독)
- 이 옵션은 form의 오염된 필드가 아닌 지정된 필드 수준에서 오염된 필드를 업데이트합니다.

```js
setValue('name', 'value', { shouldDirty: true });
```

</td>
</tr>
<tr>
<td>shouldTouch</td>
<td>

`boolean`

</td>
<td>

입력을 터치된 상태로 설정할지 여부

```js
setValue('name', 'value', { shouldTouch: true });
```

</td>
</tr>
</tbody>
</table>

## Rules

- 다음 조건만 리렌더링을 트리거합니다.

  - 값 업데이트에 의해 에러가 트리거되거나 수정되는 경우
  - `setValue`가 오염이나 터치와 같은 상태 업데이트를 유발하는 경우

- 두 번째 인수를 중첩 객체로 만드는 것보다 필드 이름을 대상으로 하는 것이 좋습니다.

```js
setValue('yourDetails.firstName', 'value'); // ✔︎ performant
setValue('yourDetails', { firstName: 'value' }); // less performant

register('nestedValue', { value: { test: 'data' } }); // 중첩된 값 등록
setValue('nestedValue.test', 'updatedData'); // ✘ 필드를 찾을 수 없습니다.
setValue('nestedValue', { test: 'updatedData' }); // ✔︎ setValue가 입력을 찾고 값을 업데이트합니다.
```

- `setValue`를 호출하기 전에 입력의 이름을 등록하는 것을 권장합니다. 필드 배열을 업데이트하려면 `useFieldArray`가 먼저 실행되고 있는지 확인해야 합니다.

  **Important:** `setValue`로 필드 배열을 업데이트하는 것은 다음 버전에서 제거됩니다. 대신 `useFieldArray`의 `replace`를 사용하세요.

```js
// 필드 배열을 업데이트할 수 있습니다.
setValue('fieldArray', [{ test: '1' }, { test: '2' }]); // ✔︎

// 등록되지 않은 입력에 setValue를 호출할 수 있습니다.
setValue('notRegisteredInput', 'value'); // ✔︎ 먼저 등록하는 것을 선호합니다.

// 다음은 단일 입력을 등록합니다. (register 호출 없이)
setValue('resultSingleNestedField', { test: '1', test2: '2' }); // 🤔

// 등록된 입력에 대해 setValue는 두 입력을 모두 올바르게 업데이트합니다.
register('notRegisteredInput.test', '1');
register('notRegisteredInput.test2', '2');
setValue('notRegisteredInput', { test: '1', test2: '2' }); // setValue를 두 번 쓰는 것에 대한 sugar syntax
```

## Examples

```jsx
// Basic
import React from 'react';
import { useForm } from 'react-hook-form';

const App = () => {
  const { register, setValue } = useForm({
    firstName: '',
  });

  return (
    <form>
      <input {...register('firstName', { required: true })} />
      <button onClick={() => setValue('firstName', 'Bill')}>setValue</button>
      <button
        onClick={() =>
          setValue('firstName', 'Luo', {
            shouldValidate: true,
            shouldDirty: true,
          })
        }
      >
        setValue options
      </button>
    </form>
  );
};
```

```tsx
// Dependant fields
import React, { useEffect } from 'react';
import { useForm } from 'react-hook-form';

type FormValues = {
  a: string;
  b: string;
  c: string;
};

export default function App() {
  const { watch, register, handleSubmit, setValue, formState } = useForm<FormValues>({
    defaultValues: {
      a: '',
      b: '',
      c: '',
    },
  });
  const onSubmit = (data: FormValues) => console.log(data);
  const [a, b] = watch(['a', 'b']);

  useEffect(() => {
    if (formState.touchedFields.a && formState.touchedFields.b && a && b) {
      setValue('c', `${a} ${b}`);
    }
  }, [setValue, a, b, formState]);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('a')} placeholder="a" />
      <input {...register('b')} placeholder="b" />
      <input {...register('c')} placeholder="c" />
      <input type="submit" />

      <button
        type="button"
        onClick={() => {
          setValue('a', 'what', { shouldTouch: true });
          setValue('b', 'ever', { shouldTouch: true });
        }}
      >
        trigger value
      </button>
    </form>
  );
}
```
