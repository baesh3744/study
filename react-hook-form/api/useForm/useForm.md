# useForm

> `UseFormProps`

`useForm`은 form을 쉽게 관리하기 위한 커스텀 훅입니다.

```js
useForm({
  mode: 'onSubmit',
  reValidateMode: 'onChange',
  defaultValues: {},
  resolver: undefined,
  context: undefined,
  criteriaMode: 'firstError',
  shouldFocusError: true,
  shouldUnregister: false,
  shouldUseNativeValidation: false,
  delayError: undefined,
});
```

## Props

### mode

> `onChange | onBlur | onSubmit | onTouched | all = 'onSubmit'`

`mode`는 form을 제출하기 전에 유효성 검사를 진행하는 시기를 설정합니다.

| 이름      | 타입     | 설명                                                                                                                                                                                                          |
| --------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| onSubmit  | `string` | submit 이벤트가 발생할 때 유효성 검사를 진행합니다. 유효하지 않은 입력은 다시 유효성 검사를 진행하기 위해 onChange 이벤트 리스너로 전달됩니다.                                                                |
| onBlur    | `string` | blur 이벤트가 발생할 때 유효성 검사를 진행합니다.                                                                                                                                                             |
| onChange  | `string` | 각 입력에서 change 이벤트가 발생할 때 유효성 검사를 진행하고 리렌더링합니다.<br><br>**!주의:** 이 설정은 성능을 떨어뜨릴 수 있습니다.                                                                         |
| onTouched | `string` | 첫 blur 이벤트가 발생할 때 유효성 검사를 진행합니다. 이후, change 이벤트가 발생할 때 유효성 검사를 진행합니다.<br><br>**Note:** When using with Controller, make sure to wire up onBlur with the render prop. |
| all       | `string` | blur와 change 이벤트가 발생할 때 유효성 검사를 진행합니다.                                                                                                                                                    |

### reValidateMode

> `onChange | onBlur | onSubmit = 'onChange'`

`reValidateMode`는 유저가 form을 제출한 후 에러가 발생한 유효하지 않은 입력이 다시 유효성 검사를 진행할 시기를 설정합니다. 기본적으로 change 이벤트가 발생할 때 다시 유효성 검사를 진행합니다.

### defaultValues

> `Record<string, any> = {}`

`defaultValues`는 컴포넌트가 처음 렌더링되고 유저와 상호 작용하기 전에 초기값을 설정할 때 사용됩니다. 모든 입력에 non-undefined 값 (e.g. 빈 문자열, null) 을 `defaultValues`로 설정하는 것을 권장합니다.

입력의 디폴트 값은 아래와 같은 방법으로 설정할 수 있습니다.

- `defaultValue / defaultChecked` ([read more from the React docs for Default Values](https://reactjs.org/docs/uncontrolled-components.html))
- `useForm()`의 `defaultValues` (전체 form의 디폴트 값)
- `Controller` 컴포넌트의 `defaultValue` 프로퍼티

`defaultValue`와 `defaultValues` 모두 설정하면, `defaultValues` 값을 사용합니다.

#### Rules

- **Important:** 적절한 디폴트 값을 설정하고 `undefined`는 피해야 합니다.

  - `undefined`는 인라인 `defaultValue / defaultChecked`에서 훅 레벨의 `defaultValues`로의 대체를 위해 예약되어 있습니다.
  - `undefined`는 제어 컴포넌트의 디폴트 상태와 충돌합니다.

- `defaultValues`는 처음 렌더링할 때 캐싱됩니다. `defaultValues`를 초기화하고 싶으면 [reset api](https://react-hook-form.com/api/useform/reset)를 사용해야 합니다.

- `defaultValues`는 `watch`, `useWatch`, `Controller`, `useController`의 `defaultValue`로 전달됩니다.

- `defaultValues`는 기본적으로 제출 결과에 포함됩니다. 포함하고 싶지 않다면 `shouldUnregister: true`를 사용해야 합니다. 즉, 입력 값이 모든 필드 내에서 호스트됩니다.

- `defaultValues`에 커스텀 객체 (e.g. moment, luxonas) 를 포함시키지 마세요. 내부 객체를 복사하는 과정에서 의도하지 않은 결과가 발생할 수 있습니다.

- form 데이터를 포함하기 위한 다른 옵션도 있습니다.

```jsx
// include hidden input
<input {...register('hidden')} type="hidden" />;

// register input with value
register('hidden', { value: 'data' });

// include data onSubmit
const onSubmit = (data) => {
  const output = {
    ...data,
    others: 'others',
  };
};
```

### context

> `object = undefined`

`context` 객체는 변경 가능하고 `resolver`의 두 번째 인자나 Yup의 `context`로 전달됩니다.

### criteriaMode

> `firstError | all = 'firstError'`

- `firstError`로 설정하면, 각 필드의 첫 번째 에러만 수집합니다.
- `all`로 설정하면, 각 필드의 모든 에러를 수집합니다.

### shouldFocusError

> `boolean = true`

`shouldFocusError`를 `true`로 설정하고 유저가 유효성 검사에 실패하는 form을 제출하면, 에러가 발생한 필드 중 첫 번째 필드를 focus합니다.

**Note:** `ref`로 등록된 필드에서만 동작합니다. `register('test')`처럼 사용자가 등록한 입력에서는 동작하지 않습니다.

**Note:** focus 순서는 등록 순서를 따릅니다.

### delayError

> `number = undefined`

이 설정은 유저에게 에러를 보여주는 것을 밀리초 단위로 지연합니다. 에러가 발생한 입력을 수정하는 즉시 에러가 사라지며 지연은 적용되지 않습니다.

### shouldUnregister

> `boolean = false`

기본적으로 입력이 제거되어도 그 값은 제거되지 않고 유지됩니다. 그러나 `shouldUnregister`를 `true`로 설정하면 입력이 언마운트되는 동안 등록이 해제됩니다.

- `shouldUnregister`는 하위 레벨의 설정을 덮어쓰는 글로벌 설정입니다. 개별 동작을 원하는 경우, `useForm`이 아니라 컴포넌트나 훅 레벨에서 설정해야 합니다.

- 디폴트 값은 `shouldUnregister: false`입니다.

  - 언마운트된 필드에는 내장된 유효성 검사를 진행하지 않습니다.

- `useForm` 레벨에서 `shouldUnregister`를 `true`로 설정하여도, 제출 결과와 `defaultValues`를 합치지는 않습니다.

- `shouldUnregister: true`로 설정하면 form이 조금 더 네이티브하게 동작합니다. form 값이 입력 안에 존재하게 됩니다.

  - 입력이 언마운트되면 값을 제거합니다.
  - 숨겨진 입력은 hidden 데이터에 적용되어야 합니다.
  - 등록된 입력만 제출 데이터에 포함됩니다.
  - 언마운트된 입력은 `useForm`이나 `useWatch`의 `useEffect`에 알려줘야 hook form이 DOM에서 입력이 언마운트되었는지 확인할 수 있습니다.

  ```jsx
  import React, { useState } from 'react';
  import { useForm, useWatch } from 'react-hook-form';

  const NotWork = () => {
    const [show, setShow] = useState(false);
    // won't get notified, need to invoke unregister
    return show && <input {...register('test')} />;
  };

  const Work = () => {
    const { show } = useWatch();
    // get notified at useEffect
    return show && <input {...register('test1')} />;
  };

  const App = () => {
    const [show, setShow] = useState(false);
    const { control } = useForm({ shouldUnregister: true });
    return (
      <div>
        {/* get notified at useForm's useEffect */}
        {show && <input {...register('test2')} />}
        <NotWork />
        <Work control={control} />
      </div>
    );
  };
  ```

### shouldUseNativeValidation

> `boolean = false`

이 설정은 [browser native validate](https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation)를 활성화합니다. 또한 CSS selectors인 `:valid`와 `:invalid`를 활성화하면 입력에 스타일을 적용하기 더 쉬워집니다. 클라이언트 유효성 검사가 비활성화되어 있어도 이들 selectors는 사용할 수 있습니다.

- `shouldUseNativeValidation`을 설정하고 form에 `novalidate`를 설정해도 CSS selectors를 사용할 수 있습니다.
- 이 기능은 register API에서만 동작하고, `useController / Controller`에서는 동작하지 않습니다.

#### Examples

```jsx
import React from 'react';
import { useForm } from 'react-hook-form';

export default function App() {
  const { register, handleSubmit } = useForm({ shouldUseNativeValidation: true });
  const onSubmit = async (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName', { required: 'Please enter your first name.' })} />
      <input type="submit" />
    </form>
  );
}
```

### resolver

> `Resolver = undefined`

`resolver`는 Yup, Zod, Joi, Superstruct, Vest같은 외부 유효성 검사 라이브러리를 사용할 수 있게 해줍니다. `resolver`의 목표는 원하는 유효성 검사 라이브러리를 원할하게 통합할 수 있도록 하는 것입니다. 라이브러리를 사용하지 않는 경우, 자신만의 로직을 작성하여 form을 검증할 수 있습니다.

```
npm install @hookform/resolvers
```

#### Props

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

`object`</td>

<td>

form의 값 모두를 포함하는 객체입니다.</td>

</tr>
<tr>
<td>context</td>
<td>

`object`

</td>
<td>

`useForm`의 설정을 전달할 수 있는 context 객체입니다. 렌더링마다 값이 변할 수 있는 mutable object입니다.

</td>
</tr>
<tr>
<td>options</td>
<td>

```
{
  criteriaMode: string,
  fields: object,
  names: string[],
}
```

</td>
<td>

유효성 검사를 통과한 필드 객체와 이름 배열, `useForm`의 `criteriaMode`를 포함하는 객체입니다.

</td>
</tr>
</tbody>
</table>

#### Rules

- 스키마 유효성 검사는 에러 보고를 위해 필드 레벨에 초점을 맞춥니다. 상위 수준 에러 보기는 그룹 체크박스같은 컴포넌트에 적용할 수 있는 직접적인 상위 수준으로만 제한됩니다.

- 이 함수는 캐싱됩니다.

- Re-validation은 사용자 상호작용 중에 한 번에 한 필드에서만 발생합니다. 라이브러리가 `error` 객체를 평가하고 그에 따라 리렌더링을 트리거합니다.

- `resolver`는 built-in validator (e.g. required, min, etc.) 와는 사용할 수 없습니다.

- 커스텀 `resolver를` 구성할 때는

  - `values`와 `errors`를 모두 가지는 객체를 반환해야 합니다. 디폴트 값은 빈 객체 (e.g. `{}`) 입니다.
  - `error` 객체의 키는 필드의 이름 값과 일치해야 합니다.

#### Examples

```jsx
import React from 'react';
import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers';
import * as yup from 'yup';

const schema = yup
  .object()
  .shape({
    name: yup.string().required(),
    age: yup.number().required(),
  })
  .required();

const App = () => {
  const { register, handleSubmit } = useForm({
    resolver: yupResolver(schema),
  });

  return (
    <form onSubmit={handleSubmit((d) => console.log(d))}>
      <input {...register('name')} />
      <input type="number" {...register('age')} />
      <input type="submit" />
    </form>
  );
};
```

#### Tip

다음 코드 스니펫을 통해 스키마를 디버깅할 수 있습니다.

```js
resolver: async (data, context, options) => {
  // 여기서 스키마를 디버깅할 수 있습니다.
  console.log('formData', data);
  console.log('validation result', await anyResolver(schema)(data, context, options));
  return anyResolver(schema)(data, context, options);
};
```
