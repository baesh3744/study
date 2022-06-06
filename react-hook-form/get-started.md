# Get Started

- [Get Started](#get-started)
  - [00. 설치](#00-설치)
  - [01. 예시](#01-예시)
  - [02. 필드 등록하기](#02-필드-등록하기)
  - [03. 유효성 검사 적용하기](#03-유효성-검사-적용하기)
  - [04. form 합치기](#04-form-합치기)
  - [05. UI 라이브러리 사용하기](#05-ui-라이브러리-사용하기)
  - [06. 외부에서 제어하는 입력 컴포넌트 사용하기](#06-외부에서-제어하는-입력-컴포넌트-사용하기)
  - [07. 전역 상태 사용하기](#07-전역-상태-사용하기)
  - [08. 에러 핸들링](#08-에러-핸들링)
  - [09. 스키마 기반 유효성 검사](#09-스키마-기반-유효성-검사)
  - [10. React Native](#10-react-native)
  - [11. TypeScript](#11-typescript)

## 00. 설치

```console
npm install react-hook-form
```

## 01. 예시

다음 코드는 기본적인 사용 예시를 보여줍니다.

```tsx
import React from 'react';
import { useForm, SubmitHandler } from 'react-hook-form';

type Inputs = {
  example: string;
  exampleRequired: string;
};

export default function App() {
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors },
  } = useForm<Inputs>();
  const onSubmit: SubmitHandler<Inputs> = (data) => console.log(data);

  console.log(watch('example'));

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input defaultValue="test" {...register('example')} />

      <input {...register('exampleRequired', { required: true })} />

      {errors.exampleRequired && <span>This filed is required</span>}

      <input type="submit" />
    </form>
  );
}
```

## 02. 필드 등록하기

React Hook Form의 핵심 개념 중 하나는 컴포넌트를 훅에 등록하는 것입니다. 이렇게 하면 유효성 검사와 제출 모두에 등록한 값을 사용할 수 있습니다.

**Note:** 각 필드에는 등록 과정에서 키로 사용할 이름이 있어야 합니다.

```tsx
import React from 'react';
import ReactDOM from 'react-dom';
import { useForm, SubmitHandler } from 'react-hook-form';

enum GenderEnum {
  female = 'female',
  male = 'male',
  other = 'other',
}

interface IFormInput {
  firstName: String;
  gender: GenderEnum;
}

export default function App() {
  const { register, handleSubmit } = useForm<IFormInput>();
  const onSubmit: SubmitHandler<IFormInput> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label>First Name</label>
      <input {...register('firstName')} />
      <label>Gender Selection</label>
      <select {...register('gender')}>
        <option value="female">female</option>
        <option value="male">male</option>
        <option value="other">other</option>
      </select>
      <input type="submit" />
    </form>
  );
}
```

## 03. 유효성 검사 적용하기

React Hook Form은 [기존 HTML 표준](https://developer.mozilla.org/en-US/docs/learn/forms/form_validation)에 맞춰 유효성 검사를 쉽게 만듭니다.

지원하는 유효성 검사 규칙은 다음과 같습니다.

- required
- min
- max
- minLength
- maxLength
- pattern
- validate

규칙에 대한 자세한 내용은 [register](./api.md#register)에 있습니다.

```tsx
import React from 'react';
import { useForm, SubmitHandler } from 'react-hook-form';

interface IFormInput {
  firstName: string;
  lastName: string;
  age: number;
}

export default function App() {
  const { register, handleSubmit } = useForm<IFormInput>();
  const onSubmit: SubmitHandler<IFormInput> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName', { required: true, maxLength: 20 })} />
      <input {...register('lastName', { pattern: /^[A-Za-z]+$/i })} />
      <input type="number" {...register('age', { min: 18, max: 99 })} />
      <input type="submit" />
    </form>
  );
}
```

## 04. form 합치기

```tsx
import React from 'react';
import { Path, useForm, UseFormRegister, SubmitHandler } from 'react-hook-form';

interface IFormValues {
  'First Name': string;
  Age: number;
}

type InputPros = {
  label: Path<IFormValues>;
  register: UseFormRegister<IFormValues>;
  required: boolean;
};

// existing component
const Input = ({ label, register, required }: InputProps) => (
  <>
    <label>{label}</label>
    <input {...register(label, { required })} />
  </>
);

const Select = React.forwardRef<
  HTMLSelectElement,
  { label: string } & ReturnType<UseFormRegister<IFormValues>>
>(({ onChange, onBlur, name, label }, ref) => (
  <>
    <label>{label}</label>
    <select name={name} ref={ref} onChange={onChange} onBlur={onBlur}>
      <option value="20">20</option>
      <option value="30">30</option>
    </select>
  </>
));

const App = () => {
  const { register, handleSubmit } = useForm<IFormValues>();

  const onSubmit: SubmitHandler<IFormValues> = (data) => {
    alert(JSON.stringify(data));
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Input label="First Name" register={register} required />
      <Select label="Age" {...register('Age')} />
      <input type="submit" />
    </form>
  );
};
```

## 05. UI 라이브러리 사용하기

외부 UI 라이브러리의 컴포넌트가 `ref`를 제공하지 않으면, `Controller` 컴포넌트를 사용해야 합니다.

```tsx
import React from 'react';
import Select from 'react-select';
import { useForm, Controller, SubmitHandler } from 'react-hook-form';
import Input from '@material-ui/core/Input';

interface IFormInput {
  firstName: string;
  lastName: string;
  iceCreamType: { label: string; value; string };
}

const App = () => {
  const { control, handleSubmit } = useForm<IFormInput>();

  const onSubmit: SubmitHandler<IFormInput> = (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        name="firstName"
        control={control}
        defaultValue=""
        render={({ field }) => <Input {...field} />}
      />
      <Controller
        name="iceCreamType"
        control={control}
        render={({ field }) => (
          <Select
            {...field}
            options={[
              { value: 'chocolate', label: 'Chocolate' },
              { value: 'strawberry', label: 'Strawberry' },
              { value: 'vanilla', label: 'Vanilla' },
            ]}
          />
        )}
      />
      <input type="submit" />
    </form>
  );
};
```

## 06. 외부에서 제어하는 입력 컴포넌트 사용하기

React Hook Form은 비제어 컴포넌트나 HTML 기본 input은 다루기 쉽지만, React-Select, AntD, Material-UI와 같은 외부 제어 컴포넌트는 다루기 어렵습니다. [Controller](./api.md#controller) 컴포넌트를 사용하면 커스텀 `register`를 적용할 수 있고 외부 컴포넌트를 쉽게 다룰 수 있습니다.

```tsx
// Component
import React from 'react';
import { useForm, Controller, SubmitHandler } from 'react-hook-form';
import { TextField, Checkbox } from '@material-ui/core';

interface IFormInputs {
  TextField: string;
  MyCheckbox: boolean;
}

function App() {
  const { handleSubmit, control, reset } = useForm<IFormInputs>();
  const onSubmit: SubmitHandler<IFormInputs> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        name="MyCheckbox"
        control={control}
        defaultValue={false}
        rules={{ required: true }}
        render={({ field }) => <Checkbox {...field} />}
      />
      <input type="submit" />
    </form>
  );
}
```

```tsx
// Hook
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

## 07. 전역 상태 사용하기

상태 관리 라이브러리와 함께 사용할 수 있습니다.

```jsx
import React from 'react';
import { useForm } from 'react-hook-form';
import { connect } from 'react-redux';
import updateAction from './actions';

export default function App(props) {
  const { register, handleSubmit, setValue } = useForm();
  const onSubmit = (data) => props.updateAction(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Input {...register('firstName')} defaultValues={props.firstName} />
      <Input {...register('lastName')} defaultValues={props.lastName} />
      <input type="submit" />
    </form>
  );
}

connect(({ firstName, lastName }) => ({ firstName, lastName }), updateAction)(YourForm);
```

## 08. 에러 핸들링

React Hook Form은 form의 에러를 보여주기 위해서 `errors` 객체를 제공합니다. 에러 유형은 주어진 유효성 검사 제약 조건을 반환합니다. 다음 코드는 required 유효성 검사 규칙을 보여줍니다.

```tsx
import React from 'react';
import { useForm, SubmitHandler } from 'react-hook-form';

interface IFormInputs {
  firstName: string;
  lastName: string;
}

export default function App() {
  const {
    register,
    formState: { errors },
    handleSubmit,
  } = useForm<IFormInputs>();
  const onSubmit: SubmitHandler<IFormInputs> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName', { required: true })} />
      <p>{errors.firstName && 'First name is required'}</p>

      <input {...register('lastName', { required: true })} />
      <p>{errors.lastName && 'Last name is required'}</p>

      <input type="submit" />
    </form>
  );
}
```

## 09. 스키마 기반 유효성 검사

React Hook Form은 Yup, Zod, Superstruct, Joi 등을 사용하여 스키마 기반 유효성 검사를 지원합니다. `useForm`에 스키마를 넘겨주면, `errors`나 유효한 결과를 반환합니다.

```tsx
import React from 'react';
import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import * as yup from 'yup';

interface IFormInputs {
  firstName: string;
  age: number;
}

const schema = yup
  .object({
    firstName: yup.string().required(),
    age: yup.number().positive().integer().required(),
  })
  .required();

export default function App() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm<IFormInputs>({
    resolver: yupResolver(schema),
  });
  const onSubmit = (data: IFormInputs) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName')} />
      <p>{errors.firstName?.message}</p>

      <input {...register('age')} />
      <p>{errors.age?.message}</p>

      <input type="submit" />
    </form>
  );
}
```

## 10. React Native

React Native에서도 동일한 성능 향상 및 효과를 얻을 수 있습니다. `Controller`로 래핑하면 입력 컴포넌트를 사용할 수 있습니다.

```jsx
import React from 'react';
import { Text, View, TextInput, Button, Alert } from 'react-native';
import { useForm, Controller } from 'react-hook-form';

export default function App() {
  const {
    control,
    handleSubmit,
    formState: { errors },
  } = useForm({
    defaultValues: {
      name: '',
    },
  });
  const onSubmit = (data) => console.log(data);

  return (
    <View>
      <Controller
        control={control}
        rules={{
          required: true,
        }}
        render={({ field: { onChange, onBlur, value } }) => (
          <TextInput style={styles.input} onBlur={onBlur} onChangeText={onChange} value={value} />
        )}
        name="name"
      />
      {errors.name && <Text>This is required.</Text>}

      <Button title="Submit" onPress={handleSubmit(onSubmit)} />
    </View>
  );
}
```

## 11. TypeScript

```tsx
import React from 'react';
import { useForm } from 'react-hook-form';

type FormData = {
  firstName: string;
  lastName: string;
};

export default function App() {
  const {
    register,
    setValue,
    handleSubmit,
    formState: { errors },
  } = useForm<FormData>();
  const onSubmit = handleSubmit((data) => console.log(data));

  return (
    <form onSubmit={onSubmit}>
      <label>First Name</label>
      <input {...register('firstName')} />
      <label>Last Name</label>
      <input {...register('lastName')} />
      <button
        type="button"
        onClick={() => {
          setValue('lastName', 'luo'); // ✔
          setValue('firstName', true); // ✘: true is not string
          errors.bill; // ✘: property bill does not exist
        }}
      >
        SetValue
      </button>
    </form>
  );
}
```
