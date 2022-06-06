# useFormState

> `({ control: Control }) => FormState`

이 커스텀 훅은 각 form 상태를 구독하고 커스텀 훅 레벨에서 리렌더링을 격리할 수 있습니다. form 상태의 구독에 관해서는 그 범위가 있으므로 다른 `useFormState`나 `useForm`에는 영향을 주지 않습니다. 이 훅을 사용하면 대규모의 복잡한 form 애플리케이션에서 리렌더링의 영향을 줄일 수 있습니다.

## Props

다음 표는 `useFormState`의 인수에 대한 정보가 포함되어 있습니다.

| 이름     | 타입                 | 설명                                                                                                |
| -------- | -------------------- | --------------------------------------------------------------------------------------------------- |
| control  | `object`             | `useForm`으로부터 제공받은 `control` 객체입니다. `FormContext`를 사용하고 있는 경우에는 옵션입니다. |
| name     | `string \| string[]` | 입력 이름 한 개나 입력 이름의 배열을 지정하거나 모든 입력의 `formState` 업데이트를 구독합니다.      |
| disabled | `boolean = false`    | 구독을 비활성화하는 옵션입니다.                                                                     |
| exact    | `boolean = false`    | 이 prop은 입력의 이름을 구독할 때 exact match를 활성화합니다.                                       |

## Return

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
<td>isDirty</td>
<td>

`boolean`

</td>
<td>

사용자가 입력을 수정하면 `true`로 설정합니다.

- `useForm`에서 모든 입력의 `defaultValues`를 제공해야 합니다. 그래야 hook form이 form의 오염 여부를 비교하기 위한 single source of truth를 가질 수 있습니다.
- 파일 형식의 input은 파일 선택과 FileList 객체를 취소할 수 있으므로 애플리케이션 레벨에서 관리해야 합니다.

</td>
</tr>
<tr>
<td>dirtyFields</td>
<td>

`object`

</td>
<td>

사용자가 변경한 필드를 갖는 객체입니다. 라이브러리가 `defaultValues`와 비교할 수 있도록 `useForm`을 통해 모든 입력의 `defaultValues`를 제공해야 합니다.

오염된 필드는 form 전체가 아닌 필드 수준에서 마크되기 때문에 `formState`의 `isDirty`로는 표현되지 않습니다. form 전체의 상태를 확인하려면 `isDirty`를 사용해야 합니다.

</td>
</tr>
<tr>
<td>touchedFields</td>
<td>

`object`

</td>
<td>

사용자가 조작한 모든 입력을 포함하는 객체입니다.

</td>
</tr>
<tr>
<td>isSubmitted</td>
<td>

`boolean`

</td>
<td>

form이 제출되면 `true`로 설정합니다. `reset` 메소드가 호출될 때까지 `true`를 유지합니다.

</td>
</tr>
<tr>
<td>isSubmitSuccessful</td>
<td>

`boolean`

</td>
<td>

`handleSubmit` 콜백 함수가 실행되는 동안 Promise rejection이나 Error 없이 form이 성공적으로 제출되었음을 나타냅니다.

</td>
</tr>
<tr>
<td>isSubmitting</td>
<td>

`boolean`

</td>
<td>

form이 현재 제출 중이면 `true`, 그렇지 않으면 `false`입니다.

</td>
</tr>
<tr>
<td>submitCount</td>
<td>

`number`

</td>
<td>

form이 제출된 횟수입니다.

</td>
</tr>
<tr>
<td>isValid</td>
<td>

`boolean`

</td>
<td>

form에 에러가 없으면 `true`로 설정합니다.

**Note:** `isValid`는 `useForm`의 `mode`에 영향을 받습니다. onChange, onTouched, onBlur 모드에서만 적용할 수 있습니다.

</td>
</tr>
<tr>
<td>isValidating</td>
<td>

`boolean`

</td>
<td>

유효성 검사를 진행하는 동안 `true`로 설정합니다.

</td>
</tr>
<tr>
<td>errors</td>
<td>

`object`

</td>
<td>

필드 에러를 갖는 객체입니다. 에러 메시지를 쉽게 얻을 수 있는 `ErrorMessage` 컴포넌트도 있습니다.

</td>
</tr>
</tbody>
</table>

## Rules

반환된 `formState`는 특정 상태가 구독되지 않은 경우 렌더링 성능을 향상시키고 추가적인 계산을 건너뛰기 위해 Proxy로 감싸져 있습니다. 따라서 구독을 활성화시키기 위해 렌더링 전에 `formState`를 해체하거나 읽어야 합니다.

```js
const { isDirty } = useFormState(); // ✔︎
const formState = useFormState(); // ✘ formState를 해체해야 합니다.
```

## Examples

```jsx
import React from 'react';
import { useForm, useFormState } from 'react-hook-form';

export default function App() {
  const { register, handleSubmit, control } = useForm({
    defaultValues: {
      firstName: 'firstName',
    },
  });
  const { dirtyFields } = useFormState({ control });
  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName')} placeholder="First Name" />
      {dirtyFields.firstName && <p>Field is dirty.</p>}

      <input type="submit" />
    </form>
  );
}
```
