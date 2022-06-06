# watch

> `(names?: string | string[] | (data, options) => void) => unknown`

`watch`는 입력을 지켜보고 값을 반환합니다. 입력의 값을 렌더링하고 조건별로 렌더링할 항목을 결정하는 데 유용합니다.

## Props

<table>
<thead>
<tr>
<th>타입</th>
<th>설명</th>
<th>예시</th>
<th>반환 값</th>
</tr>
</thead>
<tbody>
<tr>
<td>

`string`

</td>
<td>지정된 이름을 가진 입력을 감시합니다. (lodash의 get 함수와 유사합니다.)</td>
<td>

```jsx
watch('inputName');
watch('inputName', 'value');
```

</td>
<td>

`any`

</td>
</tr>
<tr>
<td>

`string[]`

</td>
<td>여러 입력을 감시합니다.</td>
<td>

```jsx
watch(['inputName1']);
watch(['field1'], { field1: '1' });
```

</td>
<td>

`unknown[]`

</td>
</tr>
<tr>
<td>

`undefined`

</td>
<td>모든 입력을 감시합니다.</td>
<td>

```jsx
watch();
watch(undefined, { field: '1' });
```

</td>
<td>

`{ [key: string]: unknown }`

</td>
</tr>
<tr>
<td>

`(data: unknown, { name: string, type: string }) => void`

</td>
<td>모든 입력을 감시하고 콜백 함수를 호출합니다.</td>
<td>

```jsx
watch((data, { name, type }) => console.log(data, name, type));
```

</td>
<td>

`{ unsubscribe: () => void }`

</td>
</tr>
</tbody>
</table>

## Rules

- `defaultValue`가 정의되어 있지 않으면, `watch`는 처음 렌더링할 때 `undefined`를 반환합니다. 왜냐하면 `watch`는 `register`가 호출되기 전에 호출되기 때문입니다. 이러한 상황을 피하기 위해서 `useForm`에서 `defaultValues`를 설정하거나 두 번째 매개변수를 통해 `defaultValue`를 설정하는 것을 권장합니다.

- `defaultValue`와 `defaultValues` 둘 다 설정되었을 경우, `defaultValue` 값을 반환합니다.

- `watch`는 app이나 form의 루트에서 리렌더링을 트리거합니다. 성능적인 이슈가 있으면 `useWatch`를 사용하세요.

- `watch` 결과는 `useEffect`의 deps 대신 렌더링 단계에 최적화되어 있습니다. 값의 업데이트를 감지하기 위해 값을 비교해주는 외부 커스텀 훅을 사용할 수 있습니다.

## Examples

```tsx
import React, { useEffect } from 'react';
import { useForm } from 'react-hook-form';

interface IFormInputs {
  name: string;
  showAge: boolean;
  age: number;
}

function App() {
  const {
    register,
    watch,
    formState: { errors },
    handleSubmit,
  } = useForm<IFormInputs>();
  const watchShowAge = watch('showAge', false); // 두 번째 매개변수로 디폴트 값을 설정할 수 있습니다.
  const watchAllFields = watch(); // 매개변수로 아무 것도 제공하지 않으면, 모든 필드를 지켜봅니다.
  const watchFields = watch(['showAge', 'age']); // 이름을 통해 타겟 필드들을 설정할 수 있습니다.

  // 콜백 버전의 watch.
  // 사용이 끝났을 때 구독을 취소하는 것은 개발자의 책임입니다.
  useEffect(() => {
    const subscription = watch((value, { name, type }) => console.log(value, name, type));
    return () => subscription.unsubscribe();
  }, [watch]);

  const onSubmit = (data: IFormInputs) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name', { required: true, maxLength: 50 })} />
      <input type="checkbox" {...register('showAge')} />
      {watchShowAge && <input type="number" {...register('age', { min: 50 })} />}
      <input type="submit" />
    </form>
  );
}
```
