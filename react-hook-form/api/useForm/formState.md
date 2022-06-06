# formState

> `Object`

`formState`는 form 전체의 상태에 대한 정보를 담고 있습니다. form application과 사용자의 상호 작용을 추적하는데 도움이 됩니다.

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

사용자가 입력을 수정한 후에 `true`로 설정합니다.

- 모든 입력의 `defaultValues`를 `useForm`에서 제공해야 합니다. 그래야 hook form이 form의 오염 여부를 비교할 수 있는 single source of truth를 가질 수 있습니다.
- 파일 선택이나 `FileList` 객체를 취소할 수 있으므로, 파일 형식의 입력은 앱 레벨에서 관리해야 합니다.

</td>
</tr>
<tr>
<td>dirtyFields</td>
<td>

`object`

</td>
<td>

사용자가 수정한 필드가 있는 객체입니다. 라이브러리가 `defaultValues`와 비교하기 위해 `useForm`을 통해 모든 입력의 `defaultValues`를 제공해야 합니다.

오염된 필드는 form 레벨이 아닌 필드 레벨에서 표시되기 때문에 `isDirty`에는 표현되지 않습니다. form의 상태를 알고 싶다면, `isDirty`를 사용해야 합니다.

</td>
</tr>
<tr>
<td>touchedFields</td>
<td>

`object`

</td>
<td>

사용자와 상호 작용한 모든 입력을 가지고 있는 객체입니다.

</td>
</tr>
<tr>
<td>isSubmitted</td>
<td>

`boolean`

</td>
<td>

form이 제출된 후에 `true`로 설정합니다. `reset` 메소드가 호출될 때까지 `true`를 유지합니다.

</td>
</tr>
<tr>
<td>isSubmitSuccessful</td>
<td>

`boolean`

</td>
<td>

`handleSubmit` 콜백 함수가 실행되는 동안 Promise rejection이나 Error 없이 성공적으로 form을 제출하였음을 나타냅니다.

</td>
</tr>
<tr>
<td>isSubmitting</td>
<td>

`boolean`

</td>
<td>

form이 제출되고 있으면 `true`, 아니면 `false`

</td>
</tr>
<tr>
<td>submitCount</td>
<td>

`number`

</td>
<td>

form이 제출된 횟수

</td>
</tr>
<tr>
<td>isValid</td>
<td>

`boolean`

</td>
<td>

form에 에러가 없으면 `true`로 설정합니다.

**Note:** `isValid`는 `useForm`의 `mode`에 영향을 받습니다. `onChange`, `onTouched`, `onBlur`에서만 적용됩니다.

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

필드 에러가 있는 객체입니다. 에러 메시지를 쉽게 검색할 수 있는 ErrorMessage 컴포넌트도 있습니다.

</td>
</tr>
</tbody>
</table>

## Rules

- `formState`는 렌더링 성능을 개선하고 특정 상태가 구독되지 않은 경우 몇몇 로직을 건너뛰기 위해 Proxy로 감싸져 있습니다. 따라서 상태를 업데이트하기 위해서는 렌더링 전에 호출하거나 읽어야 합니다.

- `formState`는 일괄 업데이트됩니다. `useEffect`를 통해 `formState`를 구독하려면 `formState` 전체를 의존 배열에 추가해야 합니다.

```jsx
useEffect(() => {
  if (formState.errors.firstName) {
    // ...
  }
}, [formState]); // ✔︎
// ✘ formState.errors는 useEffect는 트리거하지 않습니다.
```

```tsx
import React, { useEffect } from 'react';
import { useForm } from 'react-hook-form';

type FormInputs = {
  test: string;
};

export default function App() {
  const { register, handleSubmit, formState } = useForm<FormInputs>();
  const onSubmit = (data: FormInputs) => console.log(data);

  useEffect(() => {
    console.log('touchedFields', formState.touchedFields);
  }, [formState]);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('test')} />
      <input type="submit" />
    </form>
  );
}
```

- `formState`를 구독할 때 논리 연산자에 주의해야 합니다.

```jsx
// ✘ formState.isValid는 조건부로 접근됩니다.
// 따라서 Proxy는 isValid 상태의 변경 사항을 구독하지 않습니다.
return <button disabled={!formState.isDirty || !formState.isValid} />;

// ✔︎ 변경 사항을 구독하기 위해 모든 formState 값을 읽습니다.
const { isDirty, isValid } = formState;
return <button disabled={!isDirty || !isValid} />;
```

## Examples

```tsx
import React from 'react';
import { useForm } from 'react-hook-form';

type FormInputs = {
  test: string;
};

export default function App() {
  const {
    register,
    handleSubmit,
    // Proxy를 통해 form의 상태를 구독하기 위해 렌더링 전에 formState를 읽습니다.
    formState: { errors, isDirty, isSubmitting, touchedFields, submitCount },
  } = useForm<FormInputs>();
  const onSubmit = (data: FormInputs) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('test')} />
      <input types="submit" />
    </form>
  );
}
```
