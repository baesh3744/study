# reset

> `(values?: Record<string, any>, options?: Record<string, boolean>) => void`

form의 상태, 필드 레퍼런스, 구독을 초기화합니다. 옵션 매개변수가 있으며 form 상태의 부분적인 초기화를 허용합니다.

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
<td>values</td>
<td>

`object`

</td>
<td>

form의 값을 초기화하기 위한 선택적인 객체이며, 전체 `defaultValues`를 제공하는 것을 권장합니다.

</td>
</tr>
<tr>
<td>keepErrors</td>
<td>

`boolean`

</td>
<td>

모든 에러가 유지됩니다. `keepErrors`는 추가적인 사용자의 행동에 따른 결과를 보장하지 않습니다.

</td>
</tr>
<tr>
<td>keepDirty</td>
<td>

`boolean`

</td>
<td>

`dirtyFields`가 유지됩니다. 추가적인 사용자의 행동이 있을 때까지 `isDirty`는 현재 값을 유지합니다.

**Important:** 이 옵션은 form의 값은 반영하지 않고 오염된 필드만 form 상태에 반영합니다.

</td>
</tr>
<tr>
<td>keepDirtyValues</td>
<td>

`boolean`

</td>
<td>

`dirtyFields`와 `isDirty`가 유지됩니다. 오염된 필드만 최신 나머지 값으로 업데이트되지 않습니다.

**Important:** `formState`의 `dirtyFields`를 구독해야 합니다.

</td>
</tr>
<tr>
<td>keepValues</td>
<td>

`boolean`

</td>
<td>

form의 입력 값들이 유지됩니다.

</td>
</tr>
<tr>
<td>keepDefaultValues</td>
<td>

`boolean`

</td>
<td>

`useForm`을 통해 초기화된 `defaultValues`를 유지합니다.

- 원래 `defaultValues`에 대해 값을 제공하면 `isDirty`를 다시 검사합니다.
- 원래 `defaultValues`에 대해 루트 수준으로 제한된 값을 제공하면 `dirtyFields`가 업데이트됩니다.

</td>
</tr>
<tr>
<td>keepIsSubmitted</td>
<td>

`boolean`

</td>
<td>

`isSubmitted`를 유지합니다.

</td>
</tr>
<tr>
<td>keepTouched</td>
<td>

`boolean`

</td>
<td>

`isTouched`를 유지합니다.

</td>
</tr>
<tr>
<td>keepIsValid</td>
<td>

`boolean`

</td>
<td>

추가적인 사용자의 행동이 있을 때까지 `isValid`를 유지합니다.

</td>
</tr>
<tr>
<td>keepSubmitCount</td>
<td>

`boolean`

</td>
<td>

`submitCount`를 유지합니다.

</td>
</tr>
</tbody>
</table>

## Rules

- 제어 컴포넌트의 경우, `Controller` 컴포넌트의 값을 초기화하려면 `useForm`에 `defaultValues`를 전달해야 합니다.

- `reset` API에 `defaultValues`가 제공되지 않으면, HTML 네이티브 `reset` API가 호출되어 form을 복원합니다.

- `useForm`의 `useEffect`가 호출되기 전에 `reset`을 호출하지 마세요. 이는 `reset`이 form의 상태 업데이트를 플러시하라는 신호를 보내기 전에 `useForm`의 구독이 준비되어야 하기 때문입니다.

- 제출 후 `useEffect` 내부에서 초기화하는 것을 권장합니다.

```jsx
useEffect(() => {
  reset({
    data: 'test',
  });
}, [isSubmitSuccessful]);
```

## Examples

```tsx
// Uncontrolled
import React from 'react';
import { useForm } from 'react-hook-form';

interface UseFormInputs {
  firstName: string;
  lastName: string;
}

export default function App() {
  const {
    register,
    handleSubmit,
    reset,
    formState: { errors },
  } = useForm<UseFormInputs>();
  const onSubmit = (data: UseFormInputs) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label>First name</label>
      <input {...register('firstName', { required: true })} />

      <label>Last name</label>
      <input {...register('lastName')} />

      <input type="submit" />
      <input type="reset" value="Standard Reset Field Values" />
      <input type="button" onClick={() => reset()} value="Custom Reset Field Values & Errors" />
    </form>
  );
}
```

```tsx
// Controller
import React from 'react';
import { useForm, Controller } from 'react-hook-form';
import { TextField } from '@material-ui/core';

interface IFormInputs {
  firstName: string;
  lastName: string;
}

export default function App() {
  const { register, handleSubmit, reset, setValue, control } = useForm<IFormInputs>();
  const onSubmit = (data: IFormInputs) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        render={({ field }) => <TestField {...field} />}
        name="firstName"
        control={control}
        rules={{ required: true }}
        defaultValue=""
      />
      <Controller
        render={({ field }) => <TextField {...field} />}
        name="lastName"
        control={control}
        defaultValue=""
      />

      <input type="submit" />
      <input type="button" onClick={reset} />
      <input
        type="button"
        onClick={() => {
          reset({
            firstName: 'bill',
            lastName: 'luo',
          });
        }}
      />
    </form>
  );
}
```

```tsx
// Submit with reset
import React, { useEffect } from 'react';
import { useForm } from 'react-hook-form';

export default function App() {
  const {
    register,
    handleSubmit,
    reset,
    formState: { isSubmitSuccessful },
  } = useForm({
    defaultValues: {
      something: 'anything',
    },
  });

  const onSubmit = (data) => {
    // 실행 순서가 중요하므로 useEffect에서 초기화하는 것을 권장합니다.
    // reset({ ...data })
  };

  useEffect(() => {
    if (formState.isSubmitSuccessful) {
      reset({ something: '' });
    }
  }, [formState, submittedData, reset]);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('something')} />
      <input type="submit" />
    </form>
  );
}
```

```tsx
// Field array
import React, { useEffect } from 'react';
import { useForm, useFieldArray, Controller } from 'react-hook-form';

export default function App() {
  const { register, control, handleSubmit, reset } = useForm({
    defaultValues: {
      loadState: 'unloaded',
      names: [{ firstName: 'Bill', lastName: 'Luo' }],
    },
  });
  const { fields, remove } = useFieldArray({
    control,
    name: 'names',
  });

  useEffect(() => {
    reset({
      names: [
        {
          firstName: 'Bob',
          lastName: 'Actually',
        },
        {
          firstName: 'Jane',
          lastName: 'Actually',
        },
      ],
    });
  }, [reset]);

  const onSubmit = (data) => console.log('data', data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <ul>
        {fields.map((item, index) => (
          <li key={item.id}>
            <input {...register(`names.${index}.firstName`)} />

            <Controller
              render={({ field }) => <input {...field} />}
              name={`names.${index}.lastName`}
              control={control}
            />
            <button type="button" onClick={() => remove(index)}>
              Delete
            </button>
          </li>
        ))}
      </ul>

      <input type="submit" />
    </form>
  );
}
```
