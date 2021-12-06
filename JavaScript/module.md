# 모듈(Module)

## 모듈이란

개발하는 애플리케이션의 크기가 커지면서 언젠간 파일을 여러 개로 분리해야 하는 시점이 옵니다. 이때 분리된 파일 각각을 "모듈"이라고 부르는데, 모듈은 대개 클래스 하나 혹은 특정한 목적을 가진 복수의 함수로 구성된 라이브러리 하나로 구성됩니다.

<br>

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

## 일반 스크립트와 모듈의 차이 - 모든 호스트 환경

### 1. 엄격 모드로 실행됨

모듈은 항상 엄격 모드(`"use strict"`)로 실행됩니다. 선언되지 않은 변수에 값을 할당하는 등의 코드는 에러를 발생시킵니다.

```html
<script type="module">
    a = 5; // 에러
</script>
```

### 2. 모듈 레벨 스코프

모듈은 자신만의 스코프가 있습니다. 따라서 모듈 내부에서 정의한 변수나 함수는 다른 스크립트에서 접근할 수 없습니다.

```html
<!-- user.js -->
<script type="module">
    let user = "John";
</script>

<!-- hello.js -->
<script type="module">
    console.log(user); // 모듈은 변수를 공유하지 않기 때문에 `Uncaught ReferenceError: user is not defined`라는 에러가 출력됩니다.
</script>
```

외부에 공개하려는 모듈은 `export` 해야 하고, 내보내진 모듈을 가져와 사용하려면 `import` 해줘야 합니다.

```html
<!-- user.js -->
<script type="module">
    export let user = "John";
</script>

<!-- hello.js -->
<script type="module">
    import { user } from "./user.js";

    console.log(user); // John
</script>
```

참고로 브라우저 환경에서 부득이하게 window 레벨 전역 변수를 만들어야 한다면 `window` 객체에 변수를 명시적으로 할당하고 `window.user`와 같이 접근하는 방식을 취하시면 됩니다. 그런데 이 방법은 정말 필요한 경우에만 사용하길 권유합니다.

### 3. 단 한 번만 평가됨

동일한 모듈이 여러 곳에서 사용되더라도 모듈은 최초 호출 시 단 한 번만 실행됩니다. 실행 후 결과는 이 모듈을 가져가려는 모든 모듈에 내보내집니다.

```javascript
// alert.js
console.log("모듈이 평가되었습니다!");

// 동일한 모듈을 여러 모듈에서 가져오기
// 1.js
import "./alert.js"; // "모듈이 평가되었습니다!"가 출력됩니다.

// 2.js
import "./alert.js"; // 아무 일도 발생하지 않습니다.
```

실무에서는 최상위 레벨 모듈을 대개 초기화나 내부에서 쓰이는 데이터 구조를 만들고 이를 내보내 재사용하고 싶을 때 사용합니다.

객체를 내보내는 모듈을 만들어봅시다.

```javascript
// admin.js
export let admin = {
    name: "John",
};
```

이 모듈을 가져오는 모듈이 여러 개이더라도 앞서 설명한 것처럼 모듈은 최초 호출 시 단 한 번만 평가됩니다. 이떄 `admin` 객체가 만들어지고 이 모듈을 가져오는 모든 모듈에 `admin` 객체가 전달됩니다. 각 모듈에 동일한 `admin` 객체가 전달되는 것입니다.

```javascript
// 1.js
import { admin } from "./admin.js";
admin.name = "Pete";

// 2.js
import { admin } from "./admin.js";
console.log(admin.name); // Pete

// 1.js와 2.js 모두 같은 객체를 가져오므로
// 1.js에서 객체에 가한 조작을 2.js에서도 확인할 수 있습니다.
```

모듈은 단 한 번만 실행되고 실행된 모듈은 필요한 곳에 공유되므로 어느 한 모듈에서 `admin` 객체를 수정하면 다른 모듈에서도 변경사항을 확인할 수 있습니다.

이런 특징을 이용하면 "모듈 설정(configuration)"을 쉽게 할 수 있습니다. 최초로 실행되는 모듈의 객체 프로퍼티를 원하는 대로 설정하면 다른 모듈에서 이 설정을 그대로 사용할 수 있기 때문입니다.

아래 `admin.js` 모듈은 어떤 특정한 기능을 제공해주는데, 이 기능을 사용하려면 외부에서 `admin` 객체와 관련된 인증 정보를 받아와야 한다고 가정해봅시다.

```javascript
// admin.js
export let admin = {};

export function sayHi() {
    console.log(`${admin.name}님, 안녕하세요!`);
}
```

최초로 실행되는 스크립트인 `init.js`에서 `admin.name`을 설정해주었습니다. 이렇게 하면 `admin.js`를 포함한 외부 스크립트에서 `admin.name`에 저장된 정보를 볼 수 있습니다.

```javascript
// init.js
import { admin } from "./admin.js";
admin.name = "보라";
```

또 다른 모듈에서도 `admin.name`에 저장된 정보를 볼 수 있습니다.

```javascript
// other.js
import { admin, sayHi } from "./admin.js";
console.log(admin.name); // 보라
sayHi(); // 보라님, 안녕하세요!
```

### 4. import.meta

`import.meta` 객체는 현재 모듈에 대한 정보를 제공해줍니다.

호스트 환경에 따라 제공하는 정보의 내용은 다른데, 브라우저 환경에서는 스크립트의 URL 정보를 얻을 수 있습니다. HTML 안에 있는 모듈이라면, 현재 실행 중인 웹 페이지의 URL 정보를 얻을 수 있습니다.

```html
<script type="module">
    console.log(import.meta.url); // script URL (인라인 스크립트가 위치해 있는 html 페이지의 URL)
</script>
```

### 5. this는 undefined

모듈 최상위 레벨의 `this`는 `undefined`입니다.

모듈이 아닌 일반 스크립트의 `this`는 전역 객체인 것과 대조됩니다.

```html
<script>
    console.log(this); // window
</script>

<script type="module">
    console.log(this); // undefined
</script>
```

<br>

## 일반 스크립트와 모듈의 차이 - 브라우저 환경

### 1. 지연 실행

모듈 스크립트는 외부 스크립트, 인라인 스크립트와 관계없이 마치 `defer` 속성을 붙인 것처럼 항상 지연 실행됩니다.

따라서 모듈 스크립트는 아래와 같은 특징을 보입니다.

-   외부 모듈 스크립트 `<script type="module" src="...">`를 다운로드할 때 브라우저의 HTML 처리가 멈추지 않습니다. 브라우저는 외부 모듈 스크립트와 기타 리소스를 병렬적으로 불러옵니다.
-   모듈 스크립트는 HTML 문서가 완전히 준비될 때까지 대기 상태에 있다가 HTML 문서가 완전히 만들어진 이후에 실행됩니다. 모듈의 크기가 아주 작아서 HTML보다 빨리 불러온 경우에도 마찬가지입니다.
-   스크립트의 상대적 순위가 유지됩니다. 문서상 위쪽의 스크립트부터 차례로 실행됩니다.

이런 특징때문에 모듈 스크립트는 항상 완전한 HTML 페이지를 볼 수 있고 문서 내 요소에도 접근할 수 있습니다.

```html
<script type="module">
    // 모듈 스크립트는 지연 실행되기 때문에 페이지가 모두 로드되고 난 다음에 console.log 함수가 실행되므로
    // object가 정상적으로 출력됩니다.
    console.log(typeof button);
</script>

<script>
    // 일반 스크립트는 페이지가 완전히 구성되기 전이라도 바로 실행됩니다.
    // 버튼 요소가 페이지에 만들어지기 전에 접근하였기 때문에 undefined가 출력됩니다.
    console.log(typeof button);
</script>

<button id="button">Button</button>
```

위 예시에서 일반 스크립트가 모듈 스크립트보다 먼저 실행되므로, `undefined`가 먼저, `object`가 나중에 출력됩니다.

모듈을 사용할 때는 HTML 페이지가 완전히 나타난 이후에 모듈이 실행된다는 점에 유의해야 합니다. 페이지 내 특정 기능이 모듈 스크립트에 의존적인 경우, 모듈이 완전히 로딩되기 전에 페이지가 먼저 사용자에게 노출되면 사용자가 혼란을 느낄 수 있기 때문입니다. 모듈 스크립트를 불러오는 동안에는 투명 오버레이나 "로딩 인디케이터(loading indicator)"를 보여주어 사용자의 혼란을 예방해 주어야 합니다.

### 2. 인라인 스크립트의 비동기 처리

모듈이 아닌 일반 스크립트에서 `async` 속성은 외부 스크립트를 불러올 때만 유효합니다. `async` 속성이 붙은 스크립트는 로딩이 끝나면 다른 스크립트나 HTML 문서가 처리되길 기다리지 않고 바로 실행됩니다.

반면, 모듈 스크립트에서는 `async` 속성을 인라인 스크립트에도 적용할 수 있습니다.

아래 인라인 스크립트에는 `async` 속성이 붙었기 때문에 다른 스크립트나 HTML이 처리되길 기다리지 않고 바로 실행됩니다.

가져오기(`./analytics.js`) 작업이 끝나면 HTML 파싱이 끝나지 않았거나 다른 스크립트가 대기 상태에 있더라도 모듈이 바로 실행됩니다.

이런 특징은 광고나 문서 레벨 이벤트 리스너, 카운터같이 어디에도 종속되지 않는 기능을 구현할 때 유용하게 사용할 수 있습니다.

```html
<!-- 필요한 모듈(analytics.js)의 로드가 끝나면 -->
<!-- 문서나 다른 <script>가 로드되길 기다리지 않고 바로 실행됩니다. -->
<script async type="module">
    import { counter } from "./analytics.js";
    counter.count();
</script>
```

### 3. 외부 스크립트

`type="module"`이 붙은 외부 모듈 스크립트에는 두 가지 큰 특징이 있습니다.

1. `src` 속성값이 동일한 외부 스크립트는 한 번만 실행됩니다.

    ```html
    <!-- my.js는 한 번만 로드 및 실행됩니다. -->
    <script type="module" src="my.js"></script>
    <script type="module" src="my.js"></script>
    ```

2. 외부 사이트같이 다른 오리진에서 모듈 스크립트를 불러오려면 CORS 헤더가 필요합니다. 모듈이 저장되더있는 원격 서버가 `Access-Control-Allow-Origin: *` 헤더를 제공해야만 외부 모듈을 불러올 수 있습니다. 참고로 `*` 대신 fetch를 허용할 도메인을 명시할 수도 있습니다.

    ```html
    <!-- another-site.com이 Access-Control-Allow-Origin을 지원해야만 외부 모듈을 불러올 수 있습니다. -->
    <!-- 그렇지 않으면 스크립트는 실행되지 않습니다. -->
    <script type="module" src="http://other-site.com/their.js"></script>
    ```

    이 특징은 보안을 강화해 줍니다.

### 4. 경로가 없는 모듈은 금지

브라우저 환경에서 `import`는 반드시 상대 혹은 절대 URL 앞에 와야 합니다. "경로가 없는" 모듈은 허용되지 않습니다.

```javascript
import { sayHi } from "sayHi"; // Error. "./sayHi.js"와 같이 경로 정보를 지정해 주어야 합니다.
```

Node.js나 번들링 툴은 경로가 없어도 해당 모듈을 찾을 수 있는 방법을 알기 때문에 경로가 없는 모듈을 사용할 수 있습니다. 하지만 브라우저는 경로가 없는 모듈을 지원하지 않습니다.

### 5. 호환을 위한 nomodule

구식 브라우저는 `type="module"`을 해석하지 못하기 때문에 모듈 타입의 스크립트를 만나면 이를 무시하고 넘어갑니다. `nomodule` 속성을 사용하면 이런 상황을 대비할 수 있습니다.

```html
<script type="module">
    console.log("모던 브라우저를 사용하고 계시군요.");
</script>

<script nomodule>
    console.log(
        "type=module을 해석할 수 있는 브라우저는 nomodule 타입의 스크립트는 넘어갑니다. 따라서 이 console.log문은 실행되지 않습니다."
    );
    console.log(
        "오래된 브라우저를 사용하고 있다면 type=module이 붙은 스크립트는 무시됩니다. 대신 이 console.log문이 실행됩니다."
    );
</script>
```

<br>

## 빌드 툴

브라우저 환경에서 모듈을 "단독"으로 사용하는 경우는 흔치 않습니다. 대개 웹팩(Webpack)과 같은 특별한 툴을 사용해 모듈을 한 데 묶어(번들링) 프로덕션 서버에 올리는 방식을 사용합니다.

번들러를 사용하면 모듈 분해를 통제할 수 있습니다. 여기에 더하여 경로가 없는 모듈이나 CSS, HTML 포맷의 모듈을 사용할 수 있게 해준다는 장점이 있습니다.

빌드 툴의 역할은 아래와 같습니다.

1. HTML의 `<script type="module">`에 넣은 "주요 모듈(main module. 진입점 역할을 하는 모듈)"을 선택합니다.
2. "주요 모듈"에 의존하고 있는 모듈 분석을 시작으로 모듈 간의 의존 관계를 파악합니다.
3. 모듈 전체를 한 데 모아 하나의 큰 파일을 만듭니다 (설정에 따라 여러 개의 파일을 만드는 것도 가능합니다). 이 과정에서 `import`문이 번들러 내 함수로 대체되므로 기존 기능은 그대로 유지됩니다.
4. 이런 과정 중에 변형이나 최적화도 함께 수행됩니다.
    - 도달 가능하지 않은 코드는 삭제됩니다.
    - 내보내진 모듈 중 쓰임처가 없는 모듈을 삭제합니다. (가지치기. tree-shaking)
    - `console`, `debugger` 같은 개발 관련 코드를 삭제합니다.
    - 최신 자바스크립트 문법이 사용된 경우 바벨(Babel)을 사용해 동일한 기능을 하는 낮은 버전의 스크립트로 변환합니다.
    - 공백 제거, 변수 이름 줄이기 등으로 산출물의 크기를 줄입니다.

번들링 툴을 사용하면 스크립트들은 하나 혹은 여러 개의 파일로 번들링됩니다. 이때 번들링 전 스크립트에 있던 `import`, `export`문은 특별한 번들러 함수로 대체됩니다. 번들링 과정이 끝나면 기존 스크립트에서 `import`, `export`가 사라지기 때문에 `type="module"`이 필요 없어집니다. 따라서 아래와 같이 번들링 과정을 거친 스크립트는 일반 스크립트처럼 취급할 수 있습니다.

```html
<!-- 웹팩과 같은 툴로 번들링 과정을 거친 스크립트 bundle.js -->
<script src="bundle.js"></script>
```

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

https://ko.javascript.info/modules-intro
