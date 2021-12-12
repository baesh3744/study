# 데이터 타입

최신 ECMAScript 표준은 7가지 유형을 정의합니다.

| 유형      | 타입      | typeof    | 설명                                                                       | 값                                         |
| --------- | --------- | --------- | -------------------------------------------------------------------------- | ------------------------------------------ |
| 원시 타입 | Boolean   | boolean   | 논리적 참/거짓                                                             | `true` or `false`                          |
| 원시 타입 | Null      | object    | 어떤 값이 의도적으로 비어있음을 표현                                       | `null`                                     |
| 원시 타입 | Undefined | undefined | 선언 후 값이 할당되지 않은 변수에 자동으로 할당되는 값                     | `undefined`                                |
| 원시 타입 | Number    | number    | 숫자 (정수, 실수)                                                          | ±(2^53-1), `+Infinity`, `-Infinity`, `NaN` |
| 원시 타입 | BigInt    | bigint    | 숫자 (Number 타입의 범위를 벗어나는 숫자도 가능)                           |                                            |
| 원시 타입 | String    | string    | 문자열                                                                     |                                            |
| 원시 타입 | Symbol    | symbol    | 유일하고 변경 불가능한 값. 객체 프로퍼티의 키로 사용. ECMAScript6에 추가됨 |                                            |
| 객체 타입 |           |           | 식별자로 참조할 수 있는 메모리에 있는 값                                   | 객체, 함수, 배열 등                        |

<br>

## 데이터 타입이 필요한 이유

-   값을 저장할 때 확보해야 하는 메모리 공간의 크기를 결정하기 위해
-   값을 참조할 때 한 번에 읽어 들여야 할 메모리 공간의 크기를 결정하기 위해
-   메모리에서 읽어 들인 이진수를 어떻게 해석할지 결정하기 위해

<br>

## Reference

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#undefined_type

https://github.com/junh0328/prepare_frontend_interview/blob/main/JS.md#%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%83%80%EC%9E%85
