# Controller

> `Component`

React Hook Form은 비제어 컴포넌트나 네이티브 입력은 다루기 쉽지만, React-Select, AntD, Material-UI와 같은 외부에서 제어하는 컴포넌트는 다루기 어렵습니다. `Controller` 컴포넌트는 이러한 외부 컴포넌트를 다루기 쉽게 해줍니다.

## Props

다음 표는 `useController`의 인수에 대한 정보를 포함하고 있습니다.

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

`useForm`을 호출할 때 생성된 `control` 객체입니다. `FormProvider`를 사용할 때 선택 사항입니다.

</td>
</tr>
<tr>
<td>render</td>
<td>

`Function`

</td>
<td></td>
<td>

[`render prop`](https://reactjs.org/docs/render-props.html)입니다. React 엘리먼트를 반환하고 컴포넌트에 이벤트나 값을 설정할 수 있게 해주는 함수입니다. 이것은 비표준 prop을 가진 외부 제어 컴포넌트와의 통합을 단순화합니다. `onChange`, `onBlur`, `name`, `ref`, `value`와 입력의 상태를 포함하는 `fieldState`를 제공합니다.

```jsx
// Standard
<Controller
  control={control}
  name="test"
  render={({
    field: { onChange, onBlur, value, name, ref },
    fieldState: { invalid, isTouched, isDirty, error },
    formState,
  }) => (
    <Checkbox
      onBlur={onBlur} // 입력이 터치되면 알려줍니다.
      onChange={onChange} // value를 hook form으로 보냅니다.
      checked={value}
      inputRef={ref}
    />
  )}
/>
```

```jsx
// With focus
<Controller
  render={({
    field: { onChange, onBlur, value, name, ref },
    fieldState: { invalid, isTouched, isDirty, error },
  }) => (
    <TextField
      value={value}
      onChange={onChange} // value를 hook form으로 보냅니다.
      onBlur={onBlur} // 입력이 터치되면 알려줍니다.
      inputRef={ref} // input ref를 연결합니다.
    />
  )}
  name="TextField"
  control={control}
  rules={{ required: true }}
/>
```

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
- `undefined`를 가지고 `onChange`를 호출하는 것은 유효하지 않습니다. 디폴트 값이나 지워진 값으로 `null`이나 빈 문자열을 사용해야 합니다.

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

다음 표는 `Controller`가 생성하는 프로퍼티입니다.

<table>
<thead>
<tr>
<th>객체 이름</th>
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

입력의 값을 라이브러리로 전달하는 함수입니다.

- 입력의 `onChange` prop으로 할당해야 하며 `value`는 `undefined`가 되지 않아야 합니다.
- 이 prop은 `formState`를 갱신합니다. `setValue`나 필드 업데이트와 관련된 다른 API를 수동으로 호출해서는 안 됩니다.

</td>
</tr>
<tr>
<td>onBlur</td>
<td>

`() => void`

</td>
<td>

입력의 onBlur 이벤트를 라이브러리로 전달하는 함수입니다. 입력의 `onBlur` prop에 할당해야 합니다.

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

등록된 입력의 이름입니다.

</td>
</tr>
<tr>
<td>ref</td>
<td>

`React.Ref`

</td>
<td>

hook form과 입력을 연결할 때 사용하는 ref입니다. hook form이 에러를 가지는 입력을 focus할 수 있게 하기 위해 ref를 컴포넌트의 input ref에 할당해야 합니다.

</td>
</tr>
<tr>
<td rowspan=4>fieldState</td>
<td>invalid</td>
<td>

`boolean`

</td>
<td>

현재 입력이 invalid한가

</td>
</tr>
<tr>
<td>isTouched</td>
<td>

`boolean`

</td>
<td>

현재 제어되는 입력이 터치된 상태인가

</td>
</tr>
<tr>
<td>isDirty</td>
<td>

`boolean`

</td>
<td>

현재 제어되는 입력이 오염된 상태인가

</td>
</tr>
<tr>
<td>error</td>
<td>

`object`

</td>
<td>

지정된 입력의 에러

</td>
</tr>
<tr>
<td rowspan=10>formState</td>
<td>isDirty</td>
<td>

`boolean`

</td>
<td>

사용자가 입력을 수정하면 `true`로 설정합니다.

- `useForm`에서 모든 입력의 `defaultValues`를 제공해야 합니다. 그래야 hook form이 form이 오염 여부를 비교할 수 있는 single source of truth를 가질 수 있습니다.
- 파일 타입의 입력은 파일 선택을 취소하거나 FileList 객체를 다뤄야 하기 때문에 앱 레벨에서 관리해야 합니다.

</td>
</tr>
<tr>
<td>dirtyFields</td>
<td>

`object`

</td>
<td>

사용자가 수정한 필드를 가진 객체입니다. 라이브러리가 `defaultValues`와 비교하기 위해서 `useForm`을 사용해서 모든 입력의 `defaultValues`를 제공해야 합니다.

오염된 필드는 form 전체가 아닌 필드 레벨로 표시되기 때문에 `formState`의 `isDirty`로 표현되지 않습니다. 전체 form의 상태를 결정하기 위해서는 `isDirty`를 사용해야 합니다.

</td>
</tr>
<tr>
<td>touchedFields</td>
<td>

`object`

</td>
<td>

사용자와 상호작용했던 모든 입력을 포함하는 객체입니다.

</td>
</tr>
<tr>
<td>isSubmitted</td>
<td>

`boolean`

</td>
<td>

form을 제출하고 난 후 `true`로 설정합니다. `reset` 메소드가 호출될 때까지 `true`를 유지합니다.

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

**Note:** `isValid`는 `useForm`의 `mode`에 영향을 받습니다. onChange, onTouched, onBlur 모드에서만 적용할 수 있습니다.

</td>
</tr>
<tr>
<td>isValidating</td>
<td>

`boolean`

</td>
<td>

유효성 검사 중이면 `true`로 설정합니다.

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
import React from 'react';
import ReactDatePicker from 'react-datepicker';
import { TextField } from '@material-ui/core';
import { useForm, Controller } from 'react-hook-form';

type FormValues = {
  ReactDatepicker: string;
};

function App() {
  const { handleSubmit, control } = useForm<FormValues>();

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <Controller
        control={control}
        name="ReactDatepicker"
        render={({ field: { onChange, onBlur, value, ref } }) => (
          <ReactDatePicker
            onChange={onChange} // value를 hook form으로 보냅니다.
            onBlur={onBlur} // 입력이 touched/blur되었을 때 알려줍니다.
            selected={value}
          />
        )}
      />

      <input type="submit" />
    </form>
  );
}
```

## Tips

- MUI, AntD, Chakra UI와 같은 외부 제어 컴포넌트로 작업할 때, 각 prop의 책임을 인식하는 것이 중요합니다. `Controller`는 값을 보고하고 설정하며 입력에 대한 "스파이"처럼 동작합니다.

  - onChange: hook form으로 데이터를 다시 보냅니다.
  - onBlur: 입력이 상호작용했음을 보고합니다. (focus and blur)
  - value: 입력의 초기 값과 업데이트된 값을 설정합니다.
  - ref: 에러를 가진 입력이 focus되도록 허용합니다.
  - name: 입력에 고유한 이름을 줍니다.

- 입력을 다시 등록하지 마세요. `Controller`는 등록 과정을 처리하도록 설계되었습니다.

```jsx
<Controller
  name="test"
  render={({ field }) => {
    // return <input {...field} {...register('test')} />; ✘ 중복 등록
    return <input {...field} />; // ✔︎
  }}
/>
```

- onChange 중에 값을 변환하여 hook form으로 보낼 값을 커스터마이징할 수 있습니다.

```jsx
<Controller
  name="test"
  render={({ field }) => {
    // 문자열 대신 정수를 보냅니다.
    return <input {...field} onChange={(e) => field.onChange(parseInt(e.target.value))} />;
  }}
/>
```
