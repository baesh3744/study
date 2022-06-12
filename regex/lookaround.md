# Lookaround

## 0. `match` 메소드의 반환값

`str.match(regexp)` 메소드는 문자열(`str`)이 정규식(`regexp`)과 매칭되는 부분을 검색합니다.

정규식에 `g` 플래그가 포함되어 있지 않으면, `match()`는 Array를 반환합니다. 반환된 Array는 일치하는 전체 문자열을 첫 번째 요소로, 각각의 괄호 캡처 그룹에 일치하는 문자열을 이후 요소로 가집니다.

정규식에 `g` 플래그가 포함되어 있으면, 일치하는 문자열을 요소로 갖는 Array를 반환합니다. 이때, 갭처된 그룹은 반환하지 않습니다.

## 1. Lookaround, lookahead, lookbehind

### 1-1. 언제 사용할까?

정규표현식을 통해 패턴을 찾을 때 바로 전이나 다음에 다른 패턴의 존재 여부가 매칭 결과에 영향을 미치는 경우가 있습니다. 예를 들면, "'\$1000'에서 $ 뒤에 액수만 찾아라", "3.14에서 소수점 뒤에 숫자만 찾아라" 등이 있습니다. 이러한 경우에 lookahead와 lookbehind 문법을 사용할 수 있습니다. 두 문법을 묶어서 lookaround라고 합니다.

### 1-2. Lookahead와 lookbehind란?

| 표현식    | 이름                  | 의미                                                   |
| --------- | --------------------- | ------------------------------------------------------ |
| `x(?=y)`  | (Positive) lookahead  | x 바로 다음에 y가 오는 경우에만 x를 매칭시킵니다.      |
| `x(?!y)`  | Negative lookahead    | x 바로 다음에 y가 오지 않는 경우에만 x를 매칭시킵니다. |
| `(?<=y)x` | (Positive) lookbehind | x 바로 전에 y가 오는 경우에만 x를 매칭시킵니다.        |
| `(?<!y)x` | Negative lookbehind   | x 바로 전에 y가 오지 않는 경우에만 x를 매칭시킵니다.   |

Lookahead 문법은 찾고자 하는 패턴(x) 바로 다음에 조건(y)가 존재하는지를 검사하고, lookbehind 문법은 찾고자 하는 패턴(x) 바로 전에 조건(y)가 존재하는지를 검사합니다.
​

### 1-3. 특징

Lookaround 문법의 가장 큰 특징은 조건 y를 매칭 결과에 포함하지 않는다는 것입니다.

```js
'3.14'.match(/(?<=\.)\d+/); // (1) 14
'3.14'.match(/(?<!\.)\d+/); // (2) 3
```

위 코드에서 알 수 있듯이 바로 전에 소수점이 오는 숫자(1)와 소수점이 오지 않는 숫자(2)를 구할 때, 소수점은 결과에 포함되지 않습니다.

따라서 일반적인 문법 `[]`, `()`, `^` 등을 사용하여 lookaround 문법을 구현할 때는 조건 패턴에 대한 처리를 한 번 더 해주어야 합니다.

### 조건이 여러 개일 때

Lookaround 문법에서 조건 그룹을 여러 개 줄 수 있습니다.

각 조건 그룹이 다른 패턴을 가진다면, 각 패턴에 맞게 매칭이 이루어집니다.

그런데 만약 같은 조건이 여러 개 존재한다면, 매칭 결과는 한 쪽으로 치우쳐집니다. 왼쪽 조건에는 하나씩만 매칭되고 나머지는 맨 오른쪽 조건에 매칭됩니다. Lookaround 문법 밖에서 같은 패턴이 여러 개 존재할 경우, 왼쪽 조건에 대부분 매칭되는 것과는 반대입니다.

아래와 같이 lookaround 문법 안에서 숫자를 찾는 조건이 2개 존재하면 왼쪽 조건에는 숫자 1만 매칭되고 나머지는 모두 오른쪽에 매칭됩니다.

```js
'1234'.match(/(?<=(\d+)(\d+))$/); // ['1234', '1', '234']
'1234'.match(/(\d+)(\d+)$/); // ['1234', '123', '4']
```

## 2. 브라우저 호환성

Lookahead 문법의 경우, ES3에서 JavaScript 문법으로 등록되었기 때문에 모든 브라우저에서 지원합니다.

<img width="1359" alt="caniuse.com - lookahead" src="https://media.oss.navercorp.com/user/29991/files/0630c559-c2cc-4141-85b6-88f772433cb2">

> https://caniuse.com/?search=lookahead

[Lookbehind 문법은 ES2018에 새롭게 추가](https://medium.com/@xoor/whats-new-in-es2018-es9-a122b53e56a4)되어 Chrome, Firefox 등 대부분의 모던 브라우저에서는 지원합니다. 하지만 Webkit을 기반으로 하는 Safari와 Safari on iOS, IE 등에서는 아직 지원하지 않습니다.

<img width="1360" alt="caniuse.com - lookbehind" src="https://media.oss.navercorp.com/user/29991/files/604f1b78-baf4-4d4b-b63f-4b176254af07">

> https://caniuse.com/?search=lookbehind

### 2-1. 왜 Safari는 lookbehind를 지원하지 않을까?

https://bugs.webkit.org/show_bug.cgi?id=174931

위 사이트를 보면 2017년에 처음으로 lookbehind 문법에 대한 이슈를 제기하였지만, 아직 지원하고 있지 않은 상태입니다.

다만, VLang이라는 사람이 2021년 9월에 올린 댓글을 보면

> To anyone tracking this, I emailed the Assignee of this bug and he said "We plan on getting to it in the next couple of months."

라고 하는 것으로 보아, 곧 지원하지 않을까 생각합니다.

## 3. Lookbehind 문법을 지원하지 않는 경우에는...

Lookbehind 문법을 지원하지 않는 경우에 매칭되는 문자열을 찾고 싶다면, 조건 패턴까지 모두 찾아낸 뒤, 2차 작업으로 조건 패턴을 제거하는 것이 가장 직관적인 방법입니다.

예를 들어, 'a1000'과 같이 (알파벳 1개 + 숫자) 조합에서 알파벳이 `w, o, r, k, s`일 때만 숫자를 뽑아내고 싶은 경우를 생각해 봅시다. Lookbehind 문법을 지원한다면 아래와 같이 `works`를 조건으로 넣어주면 됩니다.

```js
'w1000 b1000'.match(/(?<=[works])(\d+)/g); // ['1000']
```

lookbehind 문법을 지원하지 않는 경우에는 우선 `works`까지 포함하여 검색합니다. 이때, 그룹핑의 특성을 이용하여 찾고자 하는 패턴 (예시의 경우에는 숫자) 은 그룹으로 감싸 이후 과정에서 결과를 따로 가져올 수 있도록 합니다.

```js
const str = 'w1000 b1000';

const matchedString = str.match(/[works](\d+)/); // ['w1000', '1000']
```

이후 아래와 같이 인덱스를 이용하여 원하는 결과를 도출할 수 있습니다.

```js
const onlyNumbers = matchedString[1]; // '1000'
```

만약, 매칭이 되는 패턴이 여러 개 존재하여 `g` 플래그를 사용해야 한다면, filter, map 등의 함수를 사용해 한 번 더 걸러내야 합니다.

```js
const str = 'w1000 b1000 o500 e500';

const matchedStrings = str.match(/[works](\d+)/g); // ['w1000', 'o500']
const numbersAfterWorks = matchedStrings.map((string) => string.match(/\d+/)[0]); // ['1000', '500']
```

```js
// ""로 감싸져 있지 않는 단어 찾기
const str = 'how "are" "you" doing';

const allWords = str.match(/"?[a-z]+"?/g);
const unquotedWords = allWords.filter((word) => !word.startsWith('"') && !word.endsWith('"')); // ['how', 'doing']
```

### 3-1. 문자열의 마지막 문자 조건이 존재할 때

찾고자 하는 문자열이 2~5글자의 영소문자와 '-'로 이루어져있고, '-'로 시작하거나 끝나지 않아야 한다고 가정해 봅시다.

lookbehind 문법을 사용할 수 있다면, `^(?!-)` (첫 문자가 '-'가 아니면 시작함) 와 `(?<!-)$` (마지막 문자가 '-'가 아니면 끝냄) 를 사용하여 아래와 같이 정규표현식을 작성할 수 있습니다.

```js
const regex = new RegExp(/^(?!-)([a-z-]{2,5})(?<!-)$/);

const str1 = 'ab';
console.log(regex.test(str1)); // true

const str2 = 'ab-';
console.log(regex.test(str2)); // false
```

lookbehind 문법을 사용할 수 없다면, 마지막 문자 조건을 `[^x]`을 이용해 처리할 수 있습니다.

```js
const regex = new RegExp(/^(?!-)([a-z-]{1,4})[^-]$/);

const str1 = 'ab';
console.log(regex.test(str1)); // true

const str2 = 'ab-';
console.log(regex.test(str2)); // false
```

#### 문자열의 길이에 주의할 것!!

위의 예시에서 '-'포함하지 않으려 `/^(?!-)([a-z-]{2,5})[^-]$/`와 같이 마지막 lookbehind 문법만 바꾸게 되면 `[^-]`에서 한 글자를 요구하게 되어 3 ~ 6글자를 허용하게 됩니다.

```js
const regex = new RegExp(/^(?!-)([a-z-]{2,5})[^-]$/);

const str1 = 'ab';
console.log(regex.test(str1)); // false

const str2 = 'abc';
console.log(regex.test(str2)); // true

const str3 = 'ab-';
console.log(regex.test(str3)); // false
```

따라서 `/^(?!-)([a-z-]{1,4})[^-]$/`와 같이 중간 단계에서 확인하는 문자의 개수를 하나 줄여야 합니다.

```js
const regex = new RegExp(/^(?!-)([a-z-]{1,4})[^-]$/);

const str1 = 'ab';
console.log(regex.test(str1)); // true

const str2 = 'abcde';
console.log(regex.test(str2)); // true

const str3 = 'ab-';
console.log(regex.test(str3)); // false
```

이렇듯 마지막 문자의 조건을 처리할 때는 매칭되는 문자열의 길이에 주의해야 합니다.

## 4. Reference

- https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Regular_Expressions/Assertions
- https://github.com/tc39/proposal-regexp-lookbehind
- https://exploringjs.com/deep-js/ch_regexp-lookaround-assertions.html#there-are-no-simple-alternatives-to-negative-lookaround-assertions
- https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/match
