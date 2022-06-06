# useController

> `(props?: UseControllerProps) => { field: object, fieldState: object, formState: object }`

`useController`는 `Controller`를 동작시킵니다. 또한 `Controller`와 동일한 props 및 메소드를 공유합니다. 재사용 가능한 제어 입력을 생성하는 데 유용합니다.

## Props

<table>
<thead>
<tr>
<th>이름</th>
<th>타입</th>
<th>필수</th>
<th>설명</th>
</tr>
</thead>
<tbody>
<tr>
<td>name</td>
<td>

`FieldPath`

</td>
<td>✔︎</td>
<td>

입력의 고유한 이름

</td>
</tr>
<tr>
<td>control</td>
<td>

`Control`

</td>
<td></td>
<td>

`useForm`을 호출할 때 생성된 `control` 객체입니다. `FormProvider`를 사용하고 있는 경우에는 옵션입니다.

</td>
</tr>
<tr>
<td>defaultValue</td>
<td>

`unknown`

</td>
<td></td>
<td>

**Important:** `defaultValue`나 `useForm`의 `defaultValues`에 `undefined`를 할당할 수 없습니다.

- 필드 레벨의 `defaultValue`나 `useForm`의 `defaultValues`를 설정해야 합니다. 단, `undefined`는 유효한 값이 아닙니다.
- 디폴트 값을 가진 `reset`을 호출하려면, `useForm`의 `defaultValues`를 설정해야 합니다.

</td>
</tr>
<tr>
<td>rules</td>
<td>

`Object`

</td>
<td></td>
<td>

`register`와 같은 형식을 가지는 유효성 검사 규칙입니다.

- required, min, max, minLength, maxLength, pattern, validate

```js
rules={{ required: true }}
```

</td>
</tr>
<tr>
<td>shouldUnregister</td>
<td>

`boolean = false`

</td>
<td></td>
<td>

언마운트 후, 입력의 등록이 해제되고 `defaultValues`가 제거됩니다.

</td>
</tr>
</tbody>
</table>

## Return

다음 표에는 `useController`가 생성하는 프로퍼티에 대한 정보가 포함되어 있습니다.

<table>
<thead>
<tr>
<th>객체</th>
<th>이름</th>
<th>타입</th>
<th>설명</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan=5>field</td>
<td>onChange</td>
<td>

`(value: any) => void`

</td>
<td>

입력의 값을 라이브러리로 보내는 함수입니다.

- 입력의 `onChange` prop에 할당되어야 하며, 값은 `undefined`가 되지 않아야 합니다.
- 이 prop은 `formState`를 업데이트하며, `setValue`나 필드를 업데이트하기 위한 다른 API를 호출하지 않아야 합니다.

</td>
</tr>
<tr>
<td>onBlur</td>
<td>

`() => void`

</td>
<td>

입력의 onBlur 이벤트를 라이브러리로 보내는 함수입니다. 입력의 `onBlur` prop에 할당되어야 합니다.

</td>
</tr>
<tr>
<td>value</td>
<td>

`unknown`

</td>
<td>

제어 컴포넌트의 현재 값입니다.

</td>
</tr>
<tr>
<td>name</td>
<td>

`string`

</td>
<td>

입력의 등록된 이름입니다.

</td>
</tr>
<tr>
<td>ref</td>
<td>

`React.Ref`

</td>
<td>

hook form과 입력을 연결하는데 사용되는 ref입니다. hook form이 에러가 발생한 입력을 focus할 수 있도록 컴포넌트의 input ref에 ref를 할당해야 합니다.

</td>
</tr>
<tr>
<td rowspan=4>fieldState</td>
<td>invalid</td>
<td>

`boolean`

</td>
<td>

현재 입력의 invalid 상태입니다.

</td>
</tr>
<tr>
<td>isTouched</td>
<td>

`boolean`

</td>
<td>

현재 제어 입력이 터치된 상태인가

</td>
</tr>
<tr>
<td>isDirty</td>
<td>

`boolean`

</td>
<td>

현재 제어 입력이 오염된 상태인가

</td>
</tr>
<tr>
<td>error</td>
<td>

`object`

</td>
<td>

지정된 입력의 에러입니다.

</td>
</tr>
<tr>
<td rowspan=10>formState</td>
<td>isDirty</td>
<td>

`boolean`

</td>
<td>

사용자가 입력을 수정한 후 `true`로 설정합니다.

- `useForm`에서 모든 입력에 대한 `defaultValues`를 제공해야 합니다. hook form은 `defaultValues`를 single source of truth로 가지며 form이 오염된 상태인지 판단합니다.
- 파일 타입의 입력은 파일 선택 취소나 `FileList` 객체때문에 앱 레벨에서 관리되어야 합니다.

</td>
</tr>
<tr>
<td>dirtyFields</td>
<td>

`object`

</td>
<td>

사용자가 수정한 필드를 가지는 객체입니다. 라이브러리가 `defaultValues`와 비교할 수 있도록 `useForm`을 통해 모든 입력의 `defaultValues`를 제공해야 합니다.

오염된 필드는 form 레벨이 아닌 필드 레벨에서 표시되기 때문에 `formState`의 `isDirty`로 표시되지 않습니다. form 상태를 확인하려면 `isDirty`를 사용해야 합니다.

</td>
</tr>
<tr>
<td>touchedFields</td>
<td>

`object`

</td>
<td>

사용자와 상호 작용한 모든 입력을 포함하는 객체입니다.

</td>
</tr>
<tr>
<td>isSubmitted</td>
<td>

`boolean`

</td>
<td>

form이 제출된 후 `true`로 설정합니다. `reset` 메소드가 호출될 때까지 `true`를 유지합니다.

</td>
</tr>
<tr>
<td>isSubmitSuccessful</td>
<td>

`boolean`

</td>
<td>

`handleSubmit` 콜백 함수가 실행되는 동안 Promise rejection이나 Error 없이 form을 성공적으로 제출되었음을 나타냅니다.

</td>
</tr>
<tr>
<td>isSubmitting</td>
<td>

`boolean`

</td>
<td>

form이 제출되는 중이면 `true`, 그렇지 않으면 `false`로 설정합니다.

</td>
</tr>
<tr>
<td>submitCount</td>
<td>

`number`

</td>
<td>

form을 제출한 횟수입니다.

</td>
</tr>
<tr>
<td>isValid</td>
<td>

`boolean`

</td>
<td>

form이 에러가 없으면 `true`로 설정합니다.

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

필드 에러가 있는 객체입니다. 에러 메시지를 쉽게 검색할 수 있는 `ErrorMessage` 컴포넌트도 있습니다.

</td>
</tr>
</tbody>
</table>

## Examples

```tsx
// Textfield
import React from 'react';
import { useForm, useController, UseControllerProps } from 'react-hook-form';

type FormValues = {
  FirstName: string;
};

function Input(props: UseControllerProps<FormValues>) {
  const { field, fieldState } = useController(props);

  return (
    <div>
      <input {...field} placeholder={props.name} />
      <p>{fieldState.isTouched && 'Touched'}</p>
      <p>{fieldState.isDirty && 'Dirty'}</p>
      <p>{fieldState.invalid ? 'invalid' : 'valid'}</p>
    </div>
  );
}

export default function App() {
  const { handleSubmit, control } = useForm<FormValues>({
    defaultValues: {
      FirstName: '',
    },
    mode: 'onChange',
  });
  const onSubmit = (data: FormValues) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Input control={control} name="FirstName" rules={{ required: true }} />
      <input type="submit" />
    </form>
  );
}
```

```jsx
// Checkboxes
import React, { useState } from 'react';
import { useController, useForm } from 'react-hook-form';

const Checkboxes = ({ options, control, name }) => {
  const { field } = useController({ control, name });
  const [value, setValue] = useState(field.value || []);

  return (
    <>
      {options.map((option, index) => (
        <input
          onChange={(e) => {
            const valueCopy = [...value];

            // update checkbox value
            valueCopy[index] = e.target.checked ? e.target.value : null;

            // send data to react hook form
            field.onChange(valueCopy);

            // update local state
            setValue(valueCopy);
          }}
          key={option}
          checked={value.includes(option)}
          type="checkbox"
          value={option}
        />
      ))}
    </>
  );
};

export default function App() {
  const { register, handleSubmit, control } = useForm({
    defaultValues: { controlled: [], uncontrolled: [] },
  });
  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <section>
        <h2>uncontrolled</h2>
        <input {...register('uncontrolled')} type="checkbox" value="A" />
        <input {...register('uncontrolled')} type="checkbox" value="B" />
        <input {...register('uncontrolled')} type="checkbox" value="C" />
      </section>

      <section>
        <h2>controlled</h2>
        <Checkboxes options={['a', 'b', 'c']} control={control} name="controlled" />
      </section>

      <input type="submit" />
    </form>
  );
}
```

## Tips

- MUI, AntD, Chakra UI와 같은 외부 제어 컴포넌트로 작업할 때, 각 prop의 책임 (입력을 감시, 보고, 값을 설정) 을 인식하는 것이 중요합니다.

  - onChange: hook form으로 데이터를 다시 보냅니다.
  - onBlur: 입력이 상호작용했음을 보고합니다. (focus and blur)
  - value: 입력의 초기 값과 업데이트된 값을 설정합니다.
  - ref: 에러를 가진 입력이 focus되도록 허용합니다.
  - name: 입력에 고유한 이름을 줍니다.

  State를 호스팅하고 `useController`와 결합하는 것이 좋습니다.

  ```jsx
  const { field } = useController();
  const [value, setValue] = useState(field.value);

  onChange={(event) => {
    field.onChange(parseInt(event.target.value)); // hook form으로 데이터를 다시 보냅니다.
    setValue(event.target.value); // UI state
  }}
  ```

- 입력을 다시 등록하지 마세요. `useController`는 등록 과정을 처리하도록 설계되었습니다.

```jsx
const { field } = useController({ name: 'test' });

<input {...field} />; // ✔︎
<input {...field} {...register('test')} />; // ✘ 중복 등록
```

- 컴포넌트당 하나의 `useController`를 사용하는 것이 이상적입니다. 둘 이상을 사용해야 하는 경우, prop의 이름을 변경해야 합니다. 또는 `Controller`의 사용을 고려할 수도 있습니다.

```jsx
const { field: input } = useController({ name: 'test' });
const { field: checkbox } = useController({ name: 'test1' });

<input {...input} />;
<input {...checkbox} />;
```
