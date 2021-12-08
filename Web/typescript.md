# TypeScript

## TypeScript란

TypeScript는 JavaScript에 타입을 부여한 언어로, JavaScript의 확장된 언어라고 볼 수 있습니다.

<br>

## Why TypeScript

TypeScript는 아래 2가지 관점에서 JavaScript 코드의 품질과 개발 생산성을 높일 수 있습니다.

### 1. 에러의 사전 방지

```javascript
// math.js
function sum(a, b) {
    return a + b;
}

sum(10, 20); // 30
sum("10", "20"); // "1020"
```

`sum()` 함수는 인자로 받은 두 숫자의 합을 구하는 함수입니다. 그런데 `sum()` 함수에 인자로 숫자 대신 문자열을 전달하면, 의도하지 않은 결과가 나타납니다.

TypeScript를 사용하면 이와 같은 의도하지 않은 코드의 동작을 예방할 수 있습니다.

```typescript
// math.ts
function sum(a: number, b: number) {
    return a + b;
}

sum("10", "20"); // Error: Argument of type 'string' is not assignable to parameter of type 'number'
```

### 2. 코드 가이드 및 자동 완성 (개발 생산성 향상)

```javascript
// math.js
function sum(a, b) {
    return a + b;
}

var total = sum(10, 20);
total.toLocaleString();
```

위 코드는 `sum()` 함수를 이용하여 두 숫자의 합을 구한 다음 `toLocaleString()` (특정 언어의 표현 방식에 맞게 숫자를 표기하는 API) 를 적용한 코드입니다. 여기서 `toLocaleString()`이라는 API가 어떤 역할을 하는지가 중요한게 아니라 위와 같이 코드를 작성할 때 `total`이라는 변수의 타입이 코드를 작성하는 시점에 `number`라는 것을 JavaScript가 인지하지 못하고 있는게 중요합니다.

달리 말하면, 개발자가 스스로 `sum()` 함수의 결과를 예상하고 타입이 `number`라고 가정한 상태에서 `number`의 API인 `toLocaleString()`을 코딩하게 되는 것입니다.

`total`이라는 값이 정해져 있지 않기 때문에 JavaScript Number에서 제공하는 API인 `toLocaleString()`을 일일이 작성합니다. 만약에 오탈자라도 나서 `toLocalString()`이라고 했다면 이 `math.js` 파일을 브라우저에서 실행했을 때만 오류를 확인할 수 있을 것입니다.

그런데 만약 아래와 같이 TypeScript로 작성하면 어떻게 될까요?

```typescript
function sum(a: number, b: number) {
    return a + b;
}

var total = sum(10, 20);
total.toLocaleString();
```

변수 `total`에 대한 타입이 지정되어 있기 때문에 VSCode에서 코드를 작성 중이라면 해당 타입에 대한 API를 미리 보기로 띄워줄 수 있고, API를 일일이 치는 것이 아니라 tab으로 빠르고 정확하게 작성할 수 있습니다.

<br>

## Type Aliases vs. Interfaces

`interface`에서 할 수 있는 대부분의 기능은 `type`에서도 사용 가능합니다. 가장 중요한 차이점은 `type`은 속성을 추가하기 위해서 같은 이름으로 다시 선언할 수 없지만, `interface`는 항상 확장이 가능합니다.

### 1. 확장하기

Interface는 `extends` 키워드를 통해 확장됩니다.

```typescript
interface Animal {
    name: string;
}

interface Bear extends Animal {
    honey: boolean;
}

const bear = getBear();
bear.name;
bear.honey;
```

Type은 `&`(intersection)을 통해 확장됩니다.

```typescript
type Animal = {
    name: string;
};

type Bear = Animal & {
    honey: boolean;
};

const bear = getBear();
bear.name;
bear.honey;
```

### 2. 선언적 확장

Interface는 같은 이름으로 interface를 만들면, 자동으로 기존 interface에서 새로운 필드가 추가되며 확장됩니다.

```typescript
interface Window {
    title: string;
}

interface Window {
    ts: TypeScriptAPI;
}

const src = 'const a = "Hello World"';
window.ts.transpileModule(src, {});
```

Type은 최초 생성 후, 변경이 불가능합니다.

```typescript
type Window = {
    title: string;
};

type Window = {
    ts: TypeScriptAPI;
};

// Error: Duplicate identifier 'Window'
```

<br>

## Generics

컴포넌트의 재사용성을 높이기 위해 컴포넌트 (클래스, 함수 등) 에 하나의 타입이 아닌 여러 타입을 가능하게 하는 문법입니다.

### Hello World of Generics

`identity()` 함수는 인자로 받은 값을 그대로 리턴하는 함수입니다.

제너릭이 없다면, `identity()` 함수는 특정한 타입을 가져야 합니다.

```typescript
function identity(arg: number): number {
    return arg;
}
```

저희는 `identity()` 함수의 타입을 `any`로 표현할 수도 있습니다.

```typescript
function identity(arg: any): any {
    return arg;
}
```

`any` 타입을 사용하면 인자 `arg`에 모든 타입을 받을 수 있다는 점에서 제너릭이라고 할 수 있지만, 함수가 리턴할 때 타입에 대한 정보를 잃게 됩니다. 만약 인자로 `number`를 전달하더라도, 저희가 가지는 정보는 "어떤 값이든 리턴할 수 있다"뿐입니다.

그래서 저희는 인자로 받은 타입을 기억해 두고 리턴값의 타입으로 표현해주기 위한 방법이 필요합니다. 여기서 저희는 "type variable"을 사용하는데, 이는 값보다는 타입을 나타내기 위한 특별한 변수입니다.

```typescript
function identity<Type>(arg: Type): Type {
    return arg;
}
```

제너릭 함수를 호출하는 방법에는 두 가지가 있습니다.

첫 번째는 함수를 호출할 때, 타입 인자 (type variable에 해당하는 값) 를 포함한 모든 인자를 전달하는 것입니다. 아래 예시에서는 `Type`의 값을 명시적으로 `string`으로 설정하였습니다.

```typescript
let output = identity<string>("myString");
// let output: string
```

두 번째는 "type argument interface"를 사용하는 것입니다. 즉, 컴파일러가 인자를 통해 자동으로 `Type`의 값을 설정하도록 하는 것입니다.

```typescript
let output = identity("myString");
// let output: string
```

<br>

## Reference

https://joshua1988.github.io/ts/why-ts.html#%EC%99%9C-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C%EC%9A%94

https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces
