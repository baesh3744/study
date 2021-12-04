# 모듈(Module)

## 모듈이 필요한 이유 - 캡슐화(Encapsulation)과 종속성(Dependency)

일반적으로 새로운 프로젝트 조각(기능)은 요구 사항을 충족할 때까지 기존 프로젝트와 분리되어 개발됩니다. 새로운 프로젝트 조각이 기존의 프로젝트에 도입되는 순간 기존 프로젝트와 새로운 코드 조각 사이에는 종속성이 생깁니다. 이러한 조각은 기존의 프로젝트와 함께 작동해야 하므로 이들 간에 충돌이 발생하지 않는 것이 중요합니다. 일종의 캡슐화 없이는 두 모듈이 서로 충돌하는 것은 시간 문제입니다. 이것이 C 라이브러리가 일반적으로 접두사를 갖는 이유 중 하나입니다.

```c
// c언어
#ifndef MYLIB_INIT_H
#define MYLIB_INIT_H

enum mylib_init_code {
    mylib_init_code_success,
    mylib_init_code_error
};

enum mylib_init_code mylib_init(void);

#endif // MYLIB_INIT_H
```

캡슐화는 충돌을 방지하고 개발을 용이하게 하는 데 필수적입니다.

기존 클라이언트 측 JavaScript 개발에서는 종속성과 관련된 부분은 암시적이었습니다. 즉, 코드 블록이 실행되는 지점에서 종속성이 충족되게 하는 것이 개발자의 책임이었습니다. 또한 개발자는 종속성이 올바른 순서로 충족되는지 확인해야 했습니다.

JavaScript 개발이 점점 더 복잡해짐에 따라 종속성 관리가 복잡해질 수 있습니다. JavaScript 모듈 시스템은 이러한 문제를 처리하려고 시도합니다.

<br>

## 임시 솔루션: 공개 모듈 패턴(The Revealing Module Pattern)

대부분의 모듈 시스템은 비교적 최근에 등장했습니다. 그전에는 공개 모듈 패턴이 많이 사용되었습니다.

```javascript
// 이 예제는 "Addy Osmani's JavaScript Design Patterns"에서 가져왔습니다.
var myRevealingModule = (function () {
    var privateVar = "Ben Cherry",
        publicVar = "Hey there!";

    function privateFunction() {
        console.log("Name: " + privateVar);
    }

    function publicSetName(strName) {
        privateVar = strName;
    }

    function publicGetName() {
        privateFunction();
    }

    // Reveal public pointers to
    // private functions and properties
    return {
        setName: publicSetName,
        greeting: publicVar,
        getName: publicGetName,
    };
})();

myRevealingModule.setName("Paul Kinlan");
```

ES2015에서 `let`가 등장하기 전, JavaScript의 스코프는 함수 수준에서 동작합니다. 즉, 함수 내부에서 선언된 바인딩은 범위를 벗어날 수 없습니다. 이러한 이유로 공개 모듈 패턴은 다른 많은 JavaScript 패턴과 마찬가지로 비공개 내용을 캡슐화하기 위해 함수에 의존합니다.

위 예제에서 "public" 기호는 `return`문을 통해 노출됩니다. 다른 모든 선언은 이를 둘러싸는 함수 스코프에 의해 보호받습니다. `var`이나 IIFE를 사용하는 것이 필수는 아닙니다. 기명 함수(named function) 또한 모듈로서 사용할 수 있습니다.

이 패턴은 JavaScript 패턴에서 꽤 오랫동안 사용되어 왔으며 캡슐화 문제를 상당히 잘 처리합니다. 그러나 종속성 문제에 대해서는 별로 도움이 되지 않고 `eval`을 사용하지 않으면 동일한 소스에서 다른 모듈을 포함할 수 없습니다.

### 장점

-   어디에서나 구현할 수 있을 만큼 간단합니다. - 라이브러리 없음, 언어 지원 필요 없음
-   단일 파일에 여러 모듈을 정의할 수 있습니다.

### 단점

-   프로그래밍 방식으로 모듈을 가져올 수 있는 방법이 없습니다. (`eval`을 사용하는 경우는 제외)
-   종속성은 수동으로 처리해야 합니다.
-   모듈의 비동기 로딩은 불가능합니다.
-   순환 종속성은 문제가 될 수 있습니다.
-   정적 코드 분석기에 대해 분석하기 어렵습니다.

<br>

## CommonJS

CommonJS는 서버 측 JavaScript 개발에 도움이 되는 일련의 사양을 정의하는 것을 목표로 하는 프로젝트입니다. CommonJS 팀이 해결하려고 시도하는 영역 중 하나가 모듈입니다. Node.js 개발자는 원래 CommonJS 사양을 따르려고 했지만 나중에는 반대하기로 결정합니다. 모듈과 관련하여 Node.js의 구현은 다음과 같은 영향을 많이 받습니다.

```javascript
// In circle.js
const PI = Math.PI;

exports.area = (r) => PI * r * r;
exports.circumference = (r) => 2 * PI * r;

// In some file
const circle = require("./circle.js");
console.log(`The area of a circle of radius 4 is ${circle.area(4)}`);
```

Node와 CommonJS의 모듈이 모듈 시스템과 상호 작용하기 위해서 두 요소는 필수적입니다: `require`과 `exports`.
`require`은 다른 모듈을 현재 스코프로 가져오기 위해 사용되는 함수입니다. `require`의 매개변수는 모듈의 id입니다. Node에서 id는 `node_modules` 디렉토리 안에 있는 모듈의 이름입니다. 만약, 모듈이 `node_modules` 디렉토리에 없으면, 모듈까지의 경로가 id가 됩니다. `exports`는 안에 존재하는 모든 것을 public으로 내보내는 특별한 객체입니다. 이때, 필드의 이름은 보존됩니다.

Node와 CommonJS의 차이는 `module.exports` 객체의 형태에서 발생합니다. Node에서 `module.exports`는 실제로 내보내는 특별한 객체인 반면, `exports`는 기본적으로 `module.exports`에 바인딩되는 변수일 뿐입니다. 반면에, CommonJS에는 `module.exports` 객체가 없습니다. 즉, Node에서는 `module.exports`를 거치지 않고 사전 생성된 객체를 내보낼 수 없습니다.

```javascript
// exports를 변경하면 module.exports의 바인딩이 깨지기 때문에,
// 동작하지 않습니다.
exports = (width) => {
    return {
        area: () => width * width,
    };
};

// 아래 코드는 정상적으로 동작합니다.
module.exports = (width) => {
    return {
        area: () => width * width,
    };
};
```

CommonJS는 서버 개발을 염두에 두고 설계되었습니다. 자연스럽게 API는 동기식입니다. 즉, 모듈은 소스 파일 내에서 필요한 순서대로 필요한 순간에 로드됩니다.

### 장점

-   단순합니다: 개발자는 문서를 보지 않고도 개념을 이해할 수 있습니다.
-   종속성 관리가 통합되었습니다: 모듈은 다른 모듈이 필요하고 필요한 순서대로 로드됩니다.
-   어디에서나 `require`를 호출할 수 있습니다: 모듈을 프로그래밍 방식으로 로드할 수 있습니다.
-   순환 종속성이 지원됩니다.

### 단점

-   동기 API로 인해 특정 용도 (클라이언트 개발) 에 적합하지 않습니다.
-   모듈 하나당 하나의 파일이 필요합니다.
-   브라우저는 loader library나 transpiling이 필요합니다.
-   모듈을 위한 생성자 함수가 없습니다. (Node는 이것을 지원합니다.)
-   정적 코드 분석기에 대해 분석하기 어렵습니다.

### 구현

-   Node.js
-   클라이언트 개발에서는 webpack과 browserify가 인기있습니다. Browserify는 Node-like 모듈 정의를 분석하고 모든 종속성을 전달하는 하나의 파일에 개발자의 코드를 번들로 묶기 위해 개발되었습니다. 반면에 Webpack은 공개 전에 소스 코드의 변환을 위한 복잡한 파이프라인 생성을 다루기 위해 개발되었습니다. 여기에는 CommonJS 모듈을 함께 묶는 것이 포함됩니다.

<br>

## 비동기식 모듈 정의(AMD)

AMD는 CommonJS가 채택한 방향에 불만을 품은 개발자 그룹에서 시작되었습니다. AMD는 개발 초기에 CommonJS에서 분리되었습니다. AMD와 CommonJS의 주요 차이점은 비동기 모듈 로딩 지원에 있습니다.

```javascript
// 종속 배열과 팩토리 함수를 매개변수로 define을 호출합니다.
define(["dep1", "dep2"], function (dep1, dep2) {
    // 값을 반환함으로써 모듈 값을 정의합니다.
    return function () {};
});

// 또는
define(function (require) {
    var dep1 = require("dep1"),
        dep2 = require("dep2");

    return function () {};
});
```

비동기 로딩은 JavaScript의 클로저 관용구를 사용하여 가능합니다. 요청된 모듈의 로딩이 완료되면 함수가 호출됩니다. 모듈 정의와 모듈을 가져오는 것은 동일한 함수에서 수행됩니다: 모듈이 정의될 때 종속성이 명시적으로 만들어집니다. AMD 로더는 런타임때 프로젝트에 대한 완전한 종속성 그래프를 가질 수 있습니다. 따라서 서로 의존하지 않는 라이브러리들을 동시에 로드할 수 있습니다. 이것은 좋은 UX를 위해 시작 시간(startup time)이 중요한 브라우저에게는 특히 중요한 부분입니다.

### 장점

-   비동기 로딩 (더 나은 시작 시간)
-   순환 종속성이 지원됩니다.
-   `require`와 `exports`이 양립 가능합니다.
-   종속성 관리가 완전히 통합되었습니다.
-   필요한 경우 모듈을 여러 파일로 분리할 수 있습니다.
-   생성자 함수가 지원됩니다.
-   플러그인 지원 (사용자 정의 로딩 단계)

### 단점

-   구문적으로 약간 더 복잡합니다.
-   트랜스파일되지 않은 loader library가 필요합니다.
-   정적 코드 분석기에 대해 분석하기 어렵습니다.

### 구현

-   require.js
-   Dojo

<br>

## ES2015 모듈

```javascript
// 이 예제는 "Axel Rauschmayer 블로그"에서 가져왔습니다.
// lib.js
export const sqrt = Math.sqrt;
export function square(x) {
    return x * x;
}
export function diag(x, y) {
    return sqrt(square(x) + square(y));
}

// main.js
import { square, diag } from "lib";

console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```

`import` 지시어는 모듈을 네임스페이스로 가져오는데 사용할 수 있습니다. 이 지시문은 `require`와 `define`과 대조적으로 동적이 아닙니다. 즉, 아무곳에서나 호출할 수 없습니다. 반면에 `export` 지시문을 사용하여 요소를 공개할 수 있습니다.

`import`와 `export` 지시문의 정적 특성으로 인해 정적 분석기는 코드를 실행하지 않고도 전체 종속성 트리를 구축할 수 있습니다.

ES2015는 모듈의 동적 로딩을 지원하지 않지만, 초안 사양은 다음을 지원합니다.

```javascript
System.import("some_module")
    .then((some_module) => {
        // Use some_module
    })
    .catch((error) => {
        // ...
    });
```

실제로 ES2015는 정적 모듈 로더에 대한 구문만 지정합니다. 실제로 ES2015 구현은 이러한 지시문을 분석한 후 아무 작업도 수행할 필요가 없습니다. System.js와 같은 모듈 로더는 여전히 필요합니다. 브라우저 모듈 로딩을 위한 초안 사양을 사용할 수 있습니다.

이 솔루션은 언어에 통합되어 런타임이 모듈에 가장 적합한 로딩 전략을 선택할 수 있도록 합니다. 즉, 비동기 로딩이 이점을 제공하면, 런타임에서 이를 사용할 수 있습니다.

### 업데이트 (2017년 2월)

이제 모듈의 동적 로딩에 대한 사양이 존재합니다.

### 장점

-   동기 및 비동기 로딩이 지원됩니다.
-   문법적으로 간단합니다.
-   정적 분석 도구를 지원합니다.
-   언어로 통합되었습니다: 모든 곳에서 지원되며 라이브러리가 필요없습니다.
-   순환 종속성이 지원됩니다.

<br>

## Reference

https://auth0.com/blog/javascript-module-systems-showdown/
