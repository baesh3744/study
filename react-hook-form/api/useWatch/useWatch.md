# useWatch

> `({ control?: Control, name?: string, defaultValue?: unknown, disabled?: boolean }) => object`

`watch` API와 유사하게 동작합니다. 그러나 커스텀 훅 레벨에서 리렌더링을 격리하고 애플리케이션의 성능을 향상시킬 수 있습니다.

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

`string | string[] | undefined`

</td>
<td>

필드의 이름입니다.

</td>
</tr>
<tr>
<td>control</td>
<td>

`Object`

</td>
<td>

`useForm`에서 제공받은 `control` 객체입니다. `FormContext`를 사용하고 있는 경우에는 옵션입니다.

</td>
</tr>
<tr>
<td>defaultValue</td>
<td>

`unknown`

</td>
<td>

초기 렌더링 전에 반환할 `useWatch`의 디폴트 값입니다.

**Note:** `defaultValue`가 제공될 때 첫 렌더링은 항상 `defaultValue`를 반환합니다.

</td>
</tr>
<tr>
<td>disabled</td>
<td>

`boolean = false`

</td>
<td>

구독을 비활성화하는 옵션입니다.

</td>
</tr>
<tr>
<td>exact</td>
<td>

`boolean = false`

</td>
<td>

이 prop은 입력의 이름을 구독할 때 exact match를 활성화합니다.

</td>
</tr>
</tbody>
</table>

## Rules

- `useWatch`의 초기 반환 값은 항상 `defaultValue` 또는 `useForm`의 `defaultValues` 내부에 있는 내용을 반환합니다.

- `useWatch`와 `watch`의 유일한 차이는 루트(`useForm`) 레벨 업데이트인가 커스텀 훅 레벨 업데이트인가입니다.

- `useWatch` 실행 순서가 중요합니다. 즉, 구독이 설정되기 전에 form 값을 업데이트하면 업데이트된 값은 무시됩니다.

```js
setValue('test', 'data');
useWatch({ name: 'test' }); // ✘ 값 업데이트 후에 구독이 발생했습니다. 업데이트가 수신되지 않았습니다.

useWatch({ name: 'example' }); // ✔︎ 입력 값 업데이트가 수신되고 리렌더링을 트리거합니다.
setValue('example', 'data');
```

- `useWatch` 결과는 `useEffect`의 deps 대신 렌더링 단계에 최적화되어 있습니다. 값 업데이트를 감지하기 위해 값 비교를 위한 외부 커스텀 훅을 사용할 수 있습니다.

## Examples

```tsx
// Form
import React from 'react';
import { useForm, useWatch } from 'react-hook-form';

interface FormInputs {
  firstName: string;
  lastName: string;
}

function FirstNameWatched({ control }: { control: Control<FormInputs> }) {
  const firstName = useWatch({
    control,
    name: 'firstName', // name을 제공하지 않으면 form 전체를 감시합니다. ['firstName', 'lastName']을 제공하면 두 가지 모두 감시합니다.
    defaultValue: 'default', // 렌더링 전에 디폴트 값입니다.
  });

  return <p>Watch: {firstName}</p>;
}

function App() {
  const { register, control, handleSubmit } = useForm<FormInputs>();

  const onSubmit = (data: FormInputs) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label>First Name:</label>
      <input {...register('firstName')} />
      <input {...register('lastName')} />
      <input type="submit" />

      <FirstNameWatched control={control} />
    </form>
  );
}
```

```tsx
// Advanced field array
import React from 'react';
import { useWatch } from 'react-hook-form';

function totalCal(results) {
  let totalValue = 0;

  for (const key in results) {
    for (const value in results[key]) {
      if (typeof results[key][value] === 'string') {
        const output = parseInt(results[key][value], 10);
        totalValue = totalValue + (Number.isNaN(output) ? 0 : output);
      } else {
        totalValue = totalValue + totalCal(results[key][value], totalValue);
      }
    }
  }

  return totalValue;
}

export const Calc = ({ control, setValue }) => {
  const results = useWatch({ control, name: 'test' });
  const output = totalCalc(results);

  // Field Array로 결과를 계산하기 위해 격리된 리렌더링을 진행합니다.
  console.log(results);

  setValue('total', output);

  return <p>{output}</p>;
};
```
