# 엄격 모드(Strict Mode)

## Strict mode란

JavaScript는 오랫동안 기존의 기능을 변경하지 않고 새로운 기능을 추가하면서 호환성 이슈 없이 발전해왔습니다.

덕분에 기존에 작성한 코드는 절대 망가지지 않는다는 장점이 있었지만, JavaScript 창시자들이 했던 실수나 불완전한 결정이 언어 안에 영원히 박제된다는 단점도 생겼습니다.

이런 상황은 ECMAScript5(ES5)가 등장하기 전인 2009년까지 지속되었습니다. 그런데 새롭게 제정된 ES5에서는 새로운 기능이 추가되고 기존 기능 중 일부가 변경되었습니다. 기존 기능을 변경하였기 때문에 하위 호환성 문제가 생길 수 있어 변경사항 대부분은 ES5의 기본 모드에선 활성화되지 않도록 설계되었습니다. 대신 `"use strict";`라는 특별한 지시자를 사용해 strict mode를 활성화 했을 때만 이 변경사항이 활성화되게 해놓았습니다.

Strict mode는 JavaScript의 문법을 보다 엄격히 적용하여 기존에는 무시되던 오류를 발생시킬 가능성이 높거나 JavaScript 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시킵니다. ESLint와 같은 린트 도구를 사용하여도 strict mode와 유사한 효과를 얻을 수 있습니다.

### 장점

-   실수로 전역변수를 만드는 것이 불가능합니다.
-   암묵적으로 실패한 예외를 throw하지 못하는 할당을 만듭니다.
-   삭제할 수 없는 속성을 삭제하려고 시도합니다. (시도 효과가 없을 때까지)
-   함수의 매개변수 이름은 고유해야합니다.
-   `this`는 전역 컨텍스트에서 `undefined`입니다.
-   예외를 발생시키는 몇 가지 일반적인 코딩을 잡아냅니다.
-   헷갈리거나 잘 모르는 기능을 사용할 수 없게 합니다.

### 단점

-   일부 개발자는 익숙하지 않은 기능이 많습니다.
-   `function.caller`와 `function.arguments`에 더 이상 접근할 수 없습니다.
-   서로 다른 strict mode로 작성된 스크립트를 병합하면 문제가 발생할 수 있습니다.

<br>

## `"use strict";`

지시자 `"use strict";`가 스크립트 최상단에 위치하면 스크립트 전체가 "모던한" 방식으로 동작합니다.

`"use strict";`는 반드시 스크립트 최상단에 위치시켜야 합니다. 최상단에 위치하지 않으면 strict mode가 활성화되지 않습니다. `"use strict";` 위에는 주석만 사용할 수 있습니다.

또한 `"use strict";`는 함수 본문 맨 앞에 올 수도 있는데, 이 경우에는 해당 함수만 strict mode로 실행됩니다.

`"use strict";`를 취소할 방법은 없습니다.

```javascript
// 스크립트 전체에 strict mode가 적용됩니다.
"use strict";

function foo() {
    x = 10; // Uncaught ReferenceError: x is not defined
}
foo();

// 함수 단위로 strict mode를 적용합니다.
function foo() {
    "use strict";
    x = 10; // Uncaught ReferenceError: x is not defined
}
foo();
```

<br>

## 전역에 strict mode를 적용하는 것은 피해야 합니다.

전역에 적용한 strict mode는 스크립트 단위로 적용됩니다.

```html
<!DOCTYPE html>
<html>
    <body>
        <script>
            "use strict";
        </script>
        <script>
            x = 1; // 에러가 발생하지 않습니다.
            console.log(x); // 1
        </script>
        <script>
            "use strict";

            y = 1; // Uncaught ReferenceError: y is not defined
            console.log(y);
        </script>
    </body>
</html>
```

위 예제와 같이 스크립트 단위로 적용된 strict mode는 다른 스크립트에 영향을 주지 않고 자신의 스크립트에 한정되어 적용됩니다.

Strict mode 스크립트와 non-strict mode 스크립트를 혼용하는 것은 오류를 발생시킬 수 있습니다. 특히 외부 서드 파티 라이브러리를 사용하는 경우, 라이브러리가 non-strict mode일 경우도 있기 때문에 전역에 strict mode를 적용하는 것은 바람직하지 않습니다. 이런 경우, IIFE로 스크립트 전체를 감싸서 스코프를 구분하고 IIFE의 선두에 strict mode를 적용해야 합니다.

```javascript
(function () {
    "use strict";

    // Do something...
})();
```

<br>

## 함수 단위로 strict mode를 적용하는 것도 피해야 합니다.

앞서 말한 바와 같이 함수 단위로 strict mode를 적용할 수도 있습니다. 그러나 어떤 함수는 strict mode를 적용하고 어떤 함수는 strict mode를 적용하지 않는 것은 바람직하지 않으며 모든 함수에 일일이 strict mode를 적용하는 것은 번거로운 일입니다. 그리고 strict mode가 적용된 함수가 참조할 함수 외부의 컨텍스트에 strict mode를 적용하지 않는다면 이 또한 문제가 발생할 수 있습니다.

```javascript
(function () {
    // non-strict mode
    var let = 10; // 에러가 발생하지 않습니다.

    function foo() {
        "use strict";
        let = 20; // Uncaught SyntaxError: Unexpected strict mode reserved word
    }
    foo();
})();
```

따라서 strict mode는 IIFE로 감싼 스크립트 단위로 적용하는 것이 바람직합니다.

<br>

## Strict mode가 발생시키는 에러

### 1. 암묵적 전역 변수

```javascript
(function () {
    "use strict";

    x = 1;
    console.log(x); // Uncaught ReferenceError: x is not defined
})();
```

### 2. 변수, 함수, 매개변수의 삭제

```javascript
(function () {
    "use strict";

    var x = 1;
    delete x; // Uncaught SyntaxError: Delete of an unqualified identifier in strict mode

    function foo(a) {
        delete a; // Uncaught SyntaxError: Delete of an unqualified identifier in strict mode
    }
    delete foo; // Uncaught SyntaxError: Delete of an unqualified identifier in strict mode
})();
```

### 3. 매개변수 이름의 중복

```javascript
(function () {
    "use strict";

    // Uncaught SyntaxError: Duplicate parameter name not allowed in this context
    function foo(x, x) {
        return x + x;
    }
    console.log(foo(1, 2));
})();
```

### 4. with문의 사용

```javascript
(function () {
    "use strict";

    // Uncaught SyntaxError: Strict mode code may not include a with statement
    with ({ x: 1 }) {
        console.log(x);
    }
})();
```

### 5. 일반 함수의 this

Strict mode에서 함수를 일반 함수로 호출하면 this에 undefined가 바인딩됩니다. 생성자 함수가 아닌 일반 함수 내부에서는 this를 사용할 필요가 없기 때문입니다. 이때 에러는 발생하지 않습니다.

```javascript
(function () {
    "use strict";

    function foo() {
        console.log(this); // undefined
    }
    foo();

    function Foo() {
        console.log(this); // Foo
    }
    new Foo();
})();
```

<br>

## Reference

-   [모던 JavaScript 튜토리얼 - "엄격 모드"](https://ko.javascript.info/strict-mode)
-   [Front End Interview Handbook - "`"use strict";`이 무엇인가요? 사용시 장단점이 무엇인가요?"](https://frontendinterviewhandbook.com/kr/javascript-questions/#use-strict-%EC%9D%B4-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%EC%9A%94-%EC%82%AC%EC%9A%A9%EC%8B%9C-%EC%9E%A5%EB%8B%A8%EC%A0%90%EC%9D%B4-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%EC%9A%94)
-   [PoiemaWeb - "5.16 보다 안정적인 자바스크립트 개발 환경을 위한 Strict mode"](https://poiemaweb.com/js-strict-mode)
