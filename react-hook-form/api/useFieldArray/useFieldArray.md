# useFieldArray

> `UseFieldArrayProps`

필드 배열 (동적 form) 을 다루기 위한 커스텀 훅입니다. 더 나은 사용자 경험과 성능을 제공하는 것을 위해 등장했습니다. [이 비디오](https://www.youtube.com/watch?v=Q7lrHuUfgIs)를 시청하면 성능 향상을 확인할 수 있습니다.

## Props

| 이름             | 타입          | 필수 | 설명                                                                                                                                     |
| ---------------- | ------------- | ---- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| name             | `string`      | ✔︎   | 필드 배열의 이름                                                                                                                         |
| control          | `Object`      |      | `useForm`에서 제공하는 `control` 객체. `FormContext`를 사용하고 있는 경우에는 옵션입니다.                                                |
| shouldUnregister | `boolean`     |      | 언마운트 후, 필드 배열을 등록 해제할지 여부                                                                                              |
| keyName          | `string = id` |      | `key` prop으로 사용하기 위해 자동으로 생성되는 식별자 속성의 이름. 이 prop은 더 이상 필수가 아니며 다음 주요 버전에서 제거될 예정입니다. |

### Examples

```jsx
function FieldArray() {
  const { control, register } = useForm();
  const { fields, append, prepend, remove, swap, move, insert } = useFieldArray({
    control,
    name: 'test',
  });

  return (
    {fields.map((field, index) => (
      <input
        key={field.id} // key를 field의 id로 설정하여 포함하는 것이 중요합니다.
        {...register(`test.${index}.value`)}
      />
    ))}
  );
}
```

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
<td>fields</td>
<td>

`object & { id: string }`

</td>
<td>

이 객체에는 컴포넌트의 `defaultValue`와 `key`가 포함되어 있습니다.

</td>
</tr>
<tr>
<td>append</td>
<td>

`(obj: object | object[], focusOptions) => void`

</td>
<td>

필드 끝에 입력(들)을 추가하고 focus합니다. 이 작업 중에 입력 값이 등록됩니다.

**Important:** 데이터 추가는 필수입니다.

</td>
</tr>
<tr>
<td>prepend</td>
<td>

`(obj: object | object[], focusOptions) => void`

</td>
<td>

필드 시작 부분에 입력(들)을 추가하고 focus합니다. 이 작업 중에 입력 값이 등록됩니다.

**Important:** 데이터 추가는 필수입니다.

</td>
</tr>
<tr>
<td>insert</td>
<td>

`(index: number, value: object | object[], focusOptions) => void`

</td>
<td>

특정 위치에 입력(들)을 추가하고 focus합니다.

**Important:** 데이터 추가는 필수입니다.

</td>
</tr>
<tr>
<td>swap</td>
<td>

`(from: number, to: number) => void`

</td>
<td>

입력(들)의 위치를 바꿉니다.

</td>
</tr>
<tr>
<td>move</td>
<td>

`(from: number, to: number) => void`

</td>
<td>

입력(들)을 다른 위치로 이동합니다.

</td>
</tr>
<tr>
<td>update</td>
<td>

`(index: number, obj: object) => void`

</td>
<td>

특정 위치의 입력(들)을 업데이트합니다. 업데이트된 필드는 언마운트되고 다시 마운트됩니다. 이 동작을 원하는 것이 아니면, `setValue` API를 사용하세요.

**Important:** 데이터 업데이트는 필수입니다.

</td>
</tr>
<tr>
<td>replace</td>
<td>

`(obj: object[]) => void`

</td>
<td>

전체 필드 배열 값을 바꿉니다.

</td>
</tr>
<tr>
<td>remove</td>
<td>

`(index?: number | number[]) => void`

</td>
<td>

특정 위치의 입력(들)을 제거합니다. `index`가 제공되지 않으면 모두 제거합니다.

</td>
</tr>
</tbody>
</table>

## Rules

- `useFieldArray`는 `key` prop으로 사용되는 고유 식별자 `id`를 자동으로 생성합니다. 이것이 필요한 이유에 대한 자세한 내용은 다음을 참조하세요: https://reactjs.org/docs/lists-and-keys.html#keys

  리렌더링할 때 필드가 깨지는 것을 방지하려면 인덱스가 아닌 `field.id`를 컴포넌트 키로 추가해야 합니다.

  ```jsx
  // ✔︎
  {fields.map((field, index) => <input key={field.id} ... />)}

  // ✘
  {fields.map((field, index) => <input key={index} ... />)}
  ```

- 작업을 차례로 호출할 수 없습니다. 작업은 렌더링별로 트리거되어야 합니다.

  ```jsx
  // ✘ 다음은 올바르지 않습니다.
  handleChange={() => {
    if (fields.length === 2) {
      remove(0);
    }
    append({ test: 'test' });
  }}

  // ✔︎ 다음은 올바릅니다. 다음 렌더링 후에 두 번째 작업이 트리거됩니다.
  handleChange={() => {
    append({ test: 'test' });
  }}

  useEffect(() => {
    if (fields.length === 2) {
      remove(0);
    }
  }, [fields])
  ```

- 각 `useFieldArray`는 고유하고 고유한 상태 업데이트가 있습니다. 즉, 같은 이름의 `useFieldArray`가 여러 개 있어서는 안 됩니다.

- 각 입력의 이름은 고유해야 합니다. 동일한 이름으로 checkbox나 radio를 빌드해야 하는 경우, `useController`나 `controller`와 함께 사용하세요.

- 플랫 필드 배열을 지원하지 않습니다.

- 필드 배열을 `append`, `prepend`, `insert`, `update`할 때, `obj`는 비어있는 객체가 될 수 없으며 모든 입력의 `defaultValues`를 제공해야 합니다.

  ```js
  append(); // ✘
  append({}); // ✘
  append({ firstName: 'bill', lastName: 'luo' }); // ✔︎
  ```

## TypeScript

- 입력의 이름을 등록할 때 `const`로 캐스팅해야 합니다.

  ```tsx
  <input key={field.id} {...register(`test.${index}.test` as const)} />
  ```

- 순환 참조를 지원하지 않습니다. 자세한 내용은 [Github issue](https://github.com/react-hook-form/react-hook-form/issues/4055)를 참조하세요.

- 중첩된 필드 배열의 경우, 이름으로 필드 배열을 캐스팅해야 합니다.

  ```js
  const { fields } = useFieldArray({ name: `test.${index}.keyValue` as 'test.0.keyValue' });
  ```

## Examples

```tsx
// useFieldArray
import React from 'react';
import { useForm, useFieldArray, useWatch, Control } from 'react-hook-form';

type FormValues = {
  cart: {
    name: string;
    price: number;
    quantity: number;
  }[];
};

const Total = ({ control }: { control: Control<FormInputs> }) => {
  const formValues = useWatch({
    name: 'cart',
    control,
  });
  const total = formValues.reduce(
    (acc, current) => acc + (current.price || 0) * (current.quantity || 0),
    0,
  );
  return <p>Total Amount: {total}</p>;
};

export default function App() {
  const {
    register,
    control,
    handleSubmit,
    formState: { errors },
  } = useForm<FormValues>({
    defaultValues: {
      cart: [{ name: 'test', quantity: 1, price: 23 }],
    },
    mode: 'onBlur',
  });
  const { fields, append, remove } = useFieldArray({
    name: 'cart',
    control,
  });
  const onSubmit = (data: FormValues) => console.log(data);

  return (
    <div>
      <form onSubmit={handleSubmit(onSubmit)}>
        {fields.map((field, index) => {
          return (
            <div key={field.id}>
              <section className={'section'} key={field.id}>
                <input
                  placeholder="name"
                  {...register(`cart.${index}.name` as const, {
                    required: true,
                  })}
                  className={errors?.cart?.[index]?.name ? 'error' : ''}
                />
                <input
                  placeholder="quantity"
                  type="number"
                  {...register(`cart.${index}.quantity` as const, {
                    valueAsNumber: true,
                    required: true,
                  })}
                  className={errors?.cart?.[index]?.quantity ? 'error' : ''}
                />
                <input
                  placeholder="value"
                  type="number"
                  {...register(`cart.${index}.price` as const, {
                    valueAsNumber: true,
                    required: true,
                  })}
                  className={errors?.cart?.[index]?.price ? 'error' : ''}
                />
                <button type="button" onClick={() => remove(index)}>
                  DELETE
                </button>
              </section>
            </div>
          );
        })}

        <Total control={control} />

        <button
          type="button"
          onClick={() =>
            append({
              name: '',
              quantity: 0,
              price: 0,
            })
          }
        >
          APPEND
        </button>
        <input type="submit" />
      </form>
    </div>
  );
}
```

```jsx
// Nested form
import React from 'react';
import { useForm, useFieldArray, useWatch } from 'react-hook-form';

export default function App() {
  const { control, handleSubmit } = useForm();
  const { fields, append, update } = useFieldArray({
    control,
    name: 'array',
    defaultValues: {
      array: [],
    },
  });

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      {fields.map((field, index) => (
        <Edit key={field.id} control={control} update={update} index={index} value={field} />
      ))}

      <button
        type="button"
        onClick={() => {
          append({ firstName: '' });
        }}
      >
        append
      </button>
      <input type="submit" />
    </form>
  );
}

const Display = ({ control, index }) => {
  const data = useWatch({
    control,
    name: `array.${index}`,
  });
  return <p>{data?.firstName}</p>;
};

const Edit = ({ update, index, value, control }) => {
  const { register, handleSubmit } = useForm({
    defaultValues: value,
  });

  return (
    <div>
      <Display control={control} index={index} />

      <input placeholder="first name" {...register('firstName', { required: true })} />

      <button type="button" onClick={handleSubmit((data) => update(index, data))}>
        Submit
      </button>
    </div>
  );
};
```

```tsx
// Conditional field array
import React, { FC } from 'react';
import { useForm, useWatch, useFieldArray, Control } from 'react-hook-form';

const ConditionField = ({ control, index, register }: { control: Control; index: number }) => {
  const output = useWatch<any>({
    name: 'data',
    control,
    defaultValue: 'yay! I am watching you :)',
  });

  return (
    <>
      {output[index]?.name === 'bill' && <input {...register(`data[${index}].conditional`)} />}
      <input
        {...register(`data[${index}].easyConditional`)}
        style={{ display: output[index]?.name === 'bill' ? 'block' : 'none' }}
      />
    </>
  );
};

const UseFieldArrayUnregister: FC = () => {
  type FormValues = {
    data: { name: string }[];
  };

  const { control, handleSubmit, register } = useForm<FormValues>({
    defaultValues: {
      data: [{ name: 'test' }, { name: 'test1' }, { name: 'test2' }],
    },
    mode: 'onSubmit',
    shouldUnregister: false,
  });
  const { fields } = useFieldArray({
    control,
    name: 'data',
  });
  const onSubmit = (data: FormValues) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {fields.map((data, index) => (
        <>
          <input {...register(`data[${index}].name`)} />
          <ConditionalField control={control} register={register} index={index} />
        </>
      ))}
      <input type="submit" />
    </form>
  );
};
```

```tsx
// Focus name/index
import React from 'react';
import { useForm, useFieldArray } from 'react-hook-form';

const App = () => {
  const { register, control } = useForm<{
    test: { value: string }[];
  }>({
    defaultValues: {
      test: [{ value: '1' }, { value: '2' }],
    },
  });
  const { fields, prepend, append } = useFieldArray({
    name: 'test',
    control,
  });

  return (
    <form>
      {fields.map((field, i) => (
        <input key={field.id} {...register(`test.${i}.value` as const)} />
      ))}
      <button type="button" onClick={() => prepend({ value: '' }, { focusIndex: 1 })}>
        prepend
      </button>
      <button type="button" onClick={() => append({ value: '' }, { focusName: 'test.0.value' })}>
        append
      </button>
    </form>
  );
};
```

## Tips

### 커스텀 등록

실제 입력 없이 `Controller`에 입력을 등록할 수도 있습니다. 이는 `useFieldArray`를 복잡한 데이터 구조에 빠르고 유연하게 사용하거나 실제 데이터가 입력 내부에 저장되지 않도록 합니다.

```jsx
import { useForm, useFieldArray, Controller, useWatch } from 'react-hook-form';

const ConditionalInput = ({ control, index, field }) => {
  const value = useWatch({
    name: 'test',
    control,
  });

  return (
    <Controller
      control={control}
      name={`test.${index}.firstName`}
      render={({ field }) => (value?.[index]?.checkbox === 'on' ? <input {...field} /> : null)}
    />
  );
};

function App() {
  const { control, register } = useForm();
  const { fields, append, prepend } = useFieldArray({
    control,
    name: 'test',
  });

  return (
    <form>
      {fields.map((field, index) => (
        <ConditionalInput key={field.id} {...{ control, index, field }} />
      ))}
    </form>
  );
}
```

### 제어 필드 배열

전체 필드 배열을 제어하려는 경우가 있습니다. 즉, 각 onChange는 `fields` 객체에 반영됩니다.

```tsx
import { useForm, useFieldArray } from 'react-hook-form';

export default function App() {
  const { register, handleSubmit, control, watch } = useForm<FormValues>();
  const { fields, append } = useFieldArray({
    control,
    name: 'fieldArray',
  });
  const watchFieldArray = watch('fieldArray');
  const controlledFields = fields.map((field, index) => {
    return {
      ...field,
      ...watchFieldArray[index],
    };
  });

  return (
    <form>
      {controlledFields.map((field, index) => {
        return <input {...register(`fieldArray.${index}.name` as const)} />;
      })}
    </form>
  );
}
```
