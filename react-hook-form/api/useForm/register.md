# register

> `(name: string, RegisterOptions?) => ({ onChange, onBlur, name, ref })`

`register`는 input이나 select 엘리먼트를 등록하고 React Hook Form에 유효성 검사 규칙을 적용합니다. 유효성 검사 규칙은 HTML 표준을 기반으로 커스텀 메소드도 허용합니다.

## Return

`register` 함수를 호출하고 입력의 이름을 적용하면, 아래의 메소드를 반환합니다.

| 이름     | 타입             | 설명                               |
| -------- | ---------------- | ---------------------------------- |
| onChange | `ChangeHandler`  | 입력의 change 이벤트를 구독합니다. |
| onBlur   | `ChangeHandler`  | 입력의 blur 이벤트를 구독합니다.   |
| ref      | `React.Ref<any>` | 훅을 등록하기 위한 참조            |
| name     | `string`         | 등록된 입력의 이름                 |

| 입력의 이름                    | 제출 결과                           |
| ------------------------------ | ----------------------------------- |
| `register('firstName')`        | `{ firstName: 'value' }`            |
| `register('name.firstName')`   | `{ name: { firstName: 'value' }}`   |
| `register('name.firstName.0')` | `{ name: { firstName: ['value'] }}` |

**Tip:** Register API를 호출한 후, 입력에 생기는 일:

```jsx
const { onChange, onBlur, name, ref } = register('firstName');
// 제공한 이름의 필드 경로에 대한 타입 검사를 포함합니다.

<input
  onChange={onChange} // onChange 이벤트 할당
  onBlur={onBlur} // onBlur 이벤트 할당
  name={name} // name prop 할당
  ref={ref} // ref prop 할당
/>;
// 또는
<input {...register('firstName')} />;
```

## Options

옵션을 통해 유효성 검사를 진행하고 에러 메시지를 출력할 수 있습니다.

<table>
<thead>
<tr>
<th>이름</th>
<th>설명</th>
<th>Register with validation</th>
<th>Register with validation and error message</th>
</tr>
</thead>
<tbody>
<tr>
<td>ref</td>
<td>React 엘리먼트의 ref</td>
<td>

```jsx
/* 타입
React.Ref
*/
<input {...register('test')} />
```

</td>
<td>

```jsx
/* 타입
React.Ref
*/
<input {...register('test')} />
```

</td>
</tr>
<tr>
<td>required</td>
<td>

`true`면 form이 제출되기 전에 입력이 값을 가져야 합니다. `errors` 객체에서 에러 메시지를 반환하도록 `string`을 할당할 수 있습니다.

**Note:** 이 설정은 필수 입력을 위한 웹 제한 API와 일치하며, `object`나 `array` 타입의 입력은 `validate` 함수를 사용해야 합니다.

</td>
<td>

```jsx
/* 타입
boolean
*/
<input
  {...register('test', {
    required: true,
  })}
/>
```

</td>
<td>

```jsx
/* 타입
string |
{
  value: boolean,
  message: string
}
*/
<input
  {...register('test', {
    required: 'error message', // JS only: <p>error message</p> TS only support string
  })}
/>
```

</td>
</tr>
<tr>
<td>maxLength</td>
<td>값의 최대 길이</td>
<td>

```jsx
/* 타입
number
*/
<input
  {...register('test', {
    maxLength: 2,
  })}
/>
```

</td>
<td>

```jsx
/* 타입
{
  value: number,
  message: string
}
*/
<input
  {...register('test', {
    maxLength: {
      value: 2,
      message: 'error message', // JS only: <p>error message</p> TS only support string
    },
  })}
/>
```

</td>
</tr>
<tr>
<td>minLength</td>
<td>값의 최소 길이</td>
<td>

```jsx
/* 타입
number
*/
<input
  {...register('test', {
    minLength: 1,
  })}
/>
```

</td>
<td>

```jsx
/* 타입
{
  value: number,
  message: string
}
*/
<input
  {...register('test', {
    minLength: {
      value: 1,
      message: 'error message',
    },
  })}
/>
```

</td>
</tr>
<tr>
<td>max</td>
<td>최대값</td>
<td>

```jsx
/* 타입
number
*/
<input
  type="number"
  {...register('test', {
    max: 3,
  })}
/>
```

</td>
<td>

```jsx
/* 타입
{
  value: number,
  message: string
}
*/
<input
  type="number"
  {...register('test', {
    max: {
      value: 3,
      message: 'error message',
    },
  })}
/>
```

</td>
</tr>
<tr>
<td>min</td>
<td>최소값</td>
<td>

```jsx
/* 타입
number
*/
<input
  type="number"
  {...register('test', {
    min: 3,
  })}
/>
```

</td>
<td>

```jsx
/* 타입
{
  value: number,
  message: string
}
*/
<input
  type="number"
  {...register('test', {
    min: {
      value: 3,
      message: 'error message',
    },
  })}
/>
```

</td>
</tr>
<tr>
<td>pattern</td>
<td>

Regex 패턴

**Note:** `/g` 플래그를 가진 RegExp 객체는 매칭이 발생하는 마지막 인덱스까지 추적합니다.

</td>
<td>

```jsx
/* 타입
RegExp
*/
<input
  {...register('test', {
    pattern: /[A-Za-z]{3}/,
  })}
/>
```

</td>
<td>

```jsx
/* 타입
{
  value: RegExp,
  message: string
}
*/
<input
  {...register('test', {
    pattern: {
      value: /[A-Za-z{3}]/,
      message: 'error message',
    },
  })}
/>
```

</td>
</tr>
<tr>
<td>validate</td>
<td>

`validate` 함수의 인자로 콜백 함수나 콜백 함수로 구성된 객체를 전달하여 모든 유효성 검사를 진행할 수 있습니다.

**Note:** `object`나 `array` 데이터를 입력하는 경우에는 `validate` 함수를 사용하는 것을 권장합니다. 다른 규칙은 대부분 `string`, `string[]`, `number`, `boolean` 타입에 적용됩니다.

</td>
<td>

```jsx
/* 타입
Function | Object
*/
<input
  {...register('test', {
    validate: (value) => value === '1',
  })}
/>
// 콜백 함수로 구성된 객체
<input
  {...register('test1', {
    validate: {
      positive: (v) => parseInt(v) > 0,
      lessThanTen: (v) => parseInt(v) < 10,
      checkUrl: async () => await fetch(),
    },
  })}
/>
```

</td>
<td>

```jsx
/* 타입
Function | Object
*/
<input
  {...register('test', {
    validate: (value) => value === '1' || 'error message',
  })}
/>
// 콜백 함수로 구성된 객체
<input
  {...register('test1', {
    validate: {
      positive: (v) => parseInt(v) > 0 || 'should be greater than 0',
      lessThanTen: (v) => parseInt(v) < 10 || 'should be lower than 10',
      // 비동기 검사도 가능합니다.
      checkUrl: async () => (await fetch()) || 'error message',
      messages: (v) => !v && ['test', 'test2'],
    },
  })}
/>
```

</td>
</tr>
<tr>
<td>valueAsNumber</td>
<td>

일반적으로 숫자를 반환합니다. 문제가 발생하면 `NaN`이 반환됩니다.

- **valueAs** 과정은 유효성 검사 전에 진행됩니다.
- Text input에만 적용 가능하고 지원하지만 여전히 필드 값을 숫자 타입으로 캐스트합니다.
- `defaultValue`나 `defaultValues`는 변환하지 않습니다.

</td>
<td colspan='2'>

```jsx
/* 타입
boolean
*/
<input
  type="number"
  {...register('test', {
    valueAsNumber: true,
  })}
/>
```

</td>
</tr>
<tr>
<td>valueAsDate</td>
<td>

일반적으로 Date 객체를 반환합니다. 문제가 발생하면 `null`이 반환됩니다.

- **valueAs** 과정은 유효성 검사 전에 진행됩니다.
- Text input에만 적용됩니다.
- `defaultValue`나 `defaultValues`는 변환하지 않습니다.

</td>
<td colspan='2'>

```jsx
/* 타입
boolean
*/
<input
  type="date"
  {...register('test', {
    valueAsDate: true,
  })}
/>
```

</td>
</tr>
<tr>
<td>setValueAs</td>
<td>

입력 값을 함수에 전달하여 결과를 반환합니다.

- **valueAs** 과정은 유효성 검사 전에 진행됩니다. `valueAsNumber`나 `valueAsDate`가 `true`이면 `setValueAs`는 무시됩니다.
- Text input에만 적용됩니다.
- `defaultValue`나 `defaultValues`는 변환하지 않습니다.

</td>
<td colspan='2'>

```jsx
/* 타입
<T>(value: any) => T
*/
<input
  type="number"
  {...register('test', {
    setValueAs: (v) => parseInt(v),
  })}
/>
```

</td>
</tr>
<tr>
<td>disabled</td>
<td>

`disabled`를 `true`로 설정하면 입력 값은 `undefined`가 되고 입력을 제어할 수 없습니다.

- `disabled` prop은 내장된 유효성 검사 규칙을 생략합니다.
- 스키마 유효성 검사의 경우, 입력이나 context 객체에서 반환된 `undefined` 값을 활용할 수 있습니다.

</td>
<td colspan='2'>

```jsx
/* 타입
boolean = false
*/
<input
  {...register('test', {
    disabled: true,
  })}
/>
```

</td>
</tr>
<tr>
<td>onChange</td>
<td>change 이벤트가 발생할 때 호출하는 함수</td>
<td colspan='2'>

```jsx
/* 타입
(e: SyntheticEvent) => void
*/
register('firstName', {
  onChange: (e) => console.log(e),
});
```

</td>
</tr>
<tr>
<td>onBlur</td>
<td>blur 이벤트가 발생할 때 호출하는 함수</td>
<td colspan='2'>

```jsx
/* 타입
(e: SyntheticEvent) => void
*/
register('firstName', {
  onBlur: (e) => console.log(e),
});
```

</td>
</tr>
<tr>
<td>value</td>
<td>

등록된 입력의 값을 설정합니다. 이 prop은 `useEffect` 안에서 사용하거나 한 번만 호출해야 합니다. 재실행할 때마다 사용자가 제공한 입력 값이 업데이트되거나 덮어쓰기됩니다.

</td>
<td colspan='2'>

```jsx
/* 타입
unknown
*/
register('firstName', { value: 'bill' });
```

</td>
</tr>
<tr>
<td>shouldUnregister</td>
<td>

입력이 언마운트되면 등록이 해제되고 `defaultValues`도 제거됩니다.

</td>
<td colspan='2'>

```jsx
/* 타입
boolean
*/
<input
  {...register('test', {
    shouldUnregister: true,
  })}
/>
```

</td>
</tr>
<tr>
<td>deps</td>
<td>

유효성 검사는 종속된 입력에 대해 트리거되며 `trigger`가 아닌 `register` api로만 제한됩니다.

</td>
<td colspan='2'>

```jsx
/* 타입
string | string[]
*/
<input
  {...register('test', {
    deps: ['inputA', 'inputB'],
  })}
/>
```

</td>
</tr>
</tbody>
</table>

## Rules

- radio와 checkbox를 제외하고 `name`은 필수이며 유니크해야 합니다. 입력의 이름은 점과 괄호를 모두 지원하므로 중첩 필드를 쉽게 만들 수 있습니다.

- `name`은 숫자로 시작할 수 없고 숫자만 사용할 수 없습니다.

- TypeScript 사용 일관성을 위해서만 점을 사용하고 있으므로, 대괄호 []는 배열 형식의 값에 대해 동작하지 않습니다.

```js
register('test.0.firstName'); // ✓
register('test[0]firstName'); // ✕
```

- `disabled`가 설정된 입력은 `undefined` 값을 리턴합니다. 사용자가 입력을 업데이트하지 못하도록 하려면 `readOnly`를 사용하거나 `<fieldset>` 전체에 `disabled`를 설정해야 합니다.

```tsx
import React from 'react';
import { useForm } from 'react-hook-form';

type FormValues = {
  firstName: string;
  lastName: string;
};

export default function App() {
  const { register, handleSubmit } = useForm<FormValues>();
  const onSubmit = (data: FormValues) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input readOnly {...register('firstName')} />
      <fieldset disabled>
        <input {...register('lastName')} />
      </fieldset>
      <input type="submit" />
    </form>
  );
}
```

- 필드 배열을 생성하려면 입력의 이름 뒤에 점과 숫자가 와야 합니다. e.g. `test.0.data`

- 렌더링할 때마다 이름을 변경하면 새로운 입력이 등록됩니다. 등록된 각 입력에 대해 이름을 정적으로 유지하는 것이 좋습니다.

- 언마운트가 되어도 입력의 값과 레퍼런스는 제거되지 않습니다. 대신, `unregister`를 호출하여 제거할 수 있습니다.

- `register`의 옵션은 `undefined`나 `{}`로 제거되지 않습니다. 대신, 각 속성을 업데이트해야 합니다.

```js
register('test', { required: true });
register('test', {}); // ✘
register('test', undefined); // ✘
register('test', { required: false }); // ✔︎
```

## Examples

```tsx
import React from 'react';
import { useForm } from 'react-hook-form';

type FormValues = {
  firstName: string;
  lastName: string;
  category: string;
};

export default function App() {
  const { register, handleSubmit } = useForm<FormValues>();
  const onSubmit = (data: FormValues) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName')} placeholder="First name" />
      <input {...register('lastName')} placeholder="Last name" />

      <select {...register('category')}>
        <option value="">Select...</option>
        <option value="A">Category A</option>
        <option value="B">Category B</option>
      </select>

      <input type="submit" />
    </form>
  );
}
```

## Tips

### Custom Register

`useEffect`로 입력을 등록하고 가상의 입력으로 다룰 수도 있습니다. 제어 컴포넌트의 경우, 이 과정을 처리하기 위해 `useController` 훅과 `Controller` 컴포넌트를 제공합니다.

필드를 수동으로 등록하도록 선택한 경우, `setValue`를 사용하여 입력 값을 업데이트해야 합니다.

```jsx
register('firstName', { required: true, min: 8 });

<TextInput onTextChange={(value) => setValue('lastChange', value)} />;
```

### innerRef, inputRef를 사용하는 방법

커스텀 입력 컴포넌트가 ref를 올바르게 노출하지 않은 경우, 다음과 같이 동작하도록 할 수 있습니다.

```jsx
// ref가 할당되지 않았기 때문에 동작하지 않습니다.
<TextInput {...register('test')} />;

const firstName = register('firstName', { required: true });
<TextInput
  onChange={firstName.onChange}
  onBlur={firstName.onBlur}
  inputRef={firstName.ref} // innerRef와 같은 다른 ref 이름에 대해서도 동일한 결과를 얻을 수 있습니다.
/>;

// 입력의 ref를 전달하는 올바른 방법
const Select = React.forwardRef(({ onChange, onBlur, name, label }, ref) => (
  <select name={name} ref={ref} onChange={onChange} onBlur={onBlur}>
    <option value="20">20</option>
    <option value="30">30</option>
  </select>
));
```
