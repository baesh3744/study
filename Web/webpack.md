# 웹팩(webpack)

## 웹팩이란

웹팩은 모던 JavaScript 애플리케이션을 위한 정적 모듈 번들러입니다. 모듈 번들러란 웹 애플리케이션을 구성하는 자원 (HTML, CSS, JavaScript, Images 등) 을 모두 각각의 모듈로 보고 이를 조합해 하나의 병합된 결과물을 만드는 도구를 의미합니다.

<br>

## 웹팩의 등장 배경

### 1. 파일 단위의 JavaScript 모듈 관리의 필요성

JavaScript의 변수는 기본적으로 전역 범위에서 유효합니다. 이 때문에 서로 다른 모듈에서 같은 이름의 변수를 사용한다면, 서로가 서로를 의도치 않게 변경할 수 있습니다.

이전까지는 AMD, CommonJS와 같은 라이브러리를 통해 파일 단위로 변수를 관리하고 모듈화했습니다.

그렇다면 AMD, CommonJS와 웹팩의 차이점은 무엇일까요?

#### AMD vs. CommonJS vs. Webpack

AMD 방식은 RequireJS, CommonJS 방식은 Browserify가 인기가 많았습니다. 그런데 RequireJS는 콜백 함수를 통해 모듈을 전달받는 구조였기 때문에 모듈이 많아질수록 관리하기가 힘들었고, Browserify는 Node.js로 코드를 작성해야 했기 때문에 모듈이 많아지면 Node.js를 다루는 상황이 많아져 모듈 관리를 하나로 유지하고 싶었습니다.

웹팩은 AMD와 CommonJS를 동시에 지원하고, 기본적으로 부분을 캐싱하여 변경점만 번들링하는 방식이기 때문에 속도가 빠르고 테스터러너와의 연동도 훨씬 좋습니다.

### 2. 웹 개발 작업 자동화 도구

이전에는 프론트엔드 개발 업무를 할 때 가장 많이 반복하는 작업은 코드를 수정하고 저장한 뒤 새로 고침을 누르는 것이었습니다. 그래야 화면에 변경된 내용을 볼 수 있었습니다.

이외에도 웹 서비스를 개발하고 웹 서버에 배포할 때 아래와 같은 작업들을 해야 했습니다.

-   HTML, CSS, JS 압축
-   이미지 압축
-   CSS 전처리기 변환

이러한 일들을 자동화 해주는 도구들이 필요했고, 이전에는 Grunt와 Gulp같은 도구들이 존재했습니다.

#### Grunt vs. Gulp vs. Webpack

Grunt와 Gulp는 "task runner"이고, 웹팩은 "package bundler"입니다.

-   Task runner: 반복 가능한 특정 작업을 자동화
-   Package bundler: 종속성을 가진 애플리케이션 모듈을 정적인 소스로 재생산합니다.

Gulp는 종속성 관리를 할 수 없지만, 웹팩은 종속성 관리가 가능하고, 이는 규모가 큰 프로젝트에서 빛을 발합니다. 또한 웹팩 커맨드를 실행할 때 `--watch` 옵션을 주면 변경 내용을 감시해서 적용할 수 있습니다.

### 3. 웹 애플리케이션의 빠른 로딩 속도와 높은 성능

일반적으로 5초 이내에 웹 사이트가 표시되지 않으면 대부분의 사용자들은 해당 사이트를 벗어나거나 집중력을 잃게 됩니다.

그래서 웹 사이트의 로딩 속도를 높이기 위해 많은 노력들이 있었습니다. 그 중 대표적인 방법이 브라우저에서 서버로 요청하는 파일의 숫자를 줄이는 것입니다. 파일 숫자를 줄이기 위해 파일들을 압축하고 병합하는 작업을 진행했습니다.

뿐만 아니라 초기 페이지 로딩 속도를 높이기 위해 나중에 필요한 자원들은 나중에 요청하는 "레이지 로딩"이 등장했습니다.

웹팩은 기본적으로 자원이 필요할 때, 그때그때 요청하자는 철학을 갖고 있습니다. 웹팩의 코드 스플리팅 기능을 이용하여 원하는 모듈을 원하는 타이밍에 로딩할 수 있습니다.

<br>

## 웹팩의 주요 속성

### 1. Entry

엔트리 포인트는 웹팩이 내부의 dependency graph를 생성하기 위해 사용해야 하는 모듈입니다. 웹팩은 엔트리 포인트가 직∙간접적으로 의존하는 다른 모듈과 라이브러리를 찾아냅니다.

기본값은 `./src/index.js`이지만, 웹팩 설정에서 `entry` 속성을 설정하여 다른 또는 여러 엔트리 포인트를 지정할 수 있습니다.

```javascript
// webpack.config.js
module.exports = {
    entry: "./path/to/my/entry/file.js",
};
```

### 2. Output

`output` 속성은 생성된 번들을 내보낼 위치와 이 파일의 이름을 지정하는 방법을 웹팩에 알려주는 역할을 합니다.

기본 output 파일의 경우에는 `./dist/main.js`로, 생성된 기타 파일의 경우에는 `./dist` 폴더로 설정됩니다.

```javascript
// webpack.config.js
const path = require("path");

module.exports = {
    entry: "./path/to/my/entry/file.js",
    output: {
        path: path.resolve(__dirname, "dist"),
        filename: "my-first-webpack.bundle.js",
    },
};
```

위 예제에서 `output.filename`과 `output.path` 속성을 사용하여 웹팩에 번들의 이름과 내보낼 위치를 알려주었습니다. 참고로 상단에서 가져오는 path 모듈은 파일 경로를 지정하기 위해 사용되는 core Node.js 모듈입니다.

### 3. Loaders

웹팩은 기본적으로 JavaScript와 JSON 파일만 이해합니다. 로더를 사용하면 웹팩이 다른 유형의 파일을 처리하거나, 유효한 모듈로 변환하여 애플리케이션에서 사용하거나 dependency graph에 추가합니다.

상위 수준에서 로더는 웹팩 설정에 두 가지 속성을 가집니다.

1. 변환이 필요한 파일을 식별하는 `test` 속성
2. 변환을 수행하는데 사용되는 로더를 가리키는 `use` 속성

```javascript
// webpack.config.js
const path = require("path");

module.exports = {
    output: {
        filename: "my-first-webpack.bundle.js",
    },
    module: {
        rules: [{ test: /\.txt$/, use: "raw-loader" }],
    },
};
```

위 설정에서는 `test`와 `use`라는 두 가지 필수 속성을 가진 하나의 모듈을 위해 `rules` 속성을 정의했습니다. 이는 웹팩의 컴파일러에게 다음과 같이 말합니다.

> "이봐 웹팩 컴파일러, `require()`/`import`문 내에서 ".txt" 파일로 확인되는 경로를 발견하면 번들에 추가하기 전에 `raw-loader`를 사용해서 변환해."

### 4. Plugins

로더는 특정 유형의 모듈을 변환하는 데 사용되지만, 플러그인을 활용하여 번들을 최적화하거나, assets을 관리하고, 환경 변수 주입 등과 같은 광범위한 작업을 수행할 수 있습니다.

플러그인을 사용하려면 `require()`를 통해 플러그인을 요청하고 `plugins` 배열에 추가해야 합니다. 대부분의 플러그인은 옵션을 통해 사용자가 지정할 수 있습니다. 다른 목적으로 플러그인을 여러 번 사용하도록 설정할 수 있으므로 `new` 연산자로 호출하여 플러그인의 인스턴스를 만들어야 합니다.

```javascript
// webpack.config.js
const HtmlWebpackPlugin = require("html-webpack-plugin"); // npm을 통해 설치
const webpack = require("webpack"); // 내장 플러그인에 접근하는 데 사용

module.exports = {
    module: {
        rules: [{ test: /\.txt$/, use: "raw-loader" }],
    },
    plugins: [new HtmlWebpackPlugin({ template: "./src/index.html" })],
};
```

위 예제에서 `html-webpack-plugin`은 생성된 모든 번들을 자동으로 삽입하여 애플리케이션용 HTML 파일을 생성합니다.

### 5. Mode

`mode` 파라미터를 `development`, `production` 또는 `none`으로 설정하면 웹팩에 내장된 환경별 최적화를 활성화할 수 있습니다. 기본값은 `production`입니다.

```javascript
module.exports = {
    mode: "production",
};
```

<br>

## Reference

https://webpack.kr/concepts/

https://velog.io/@kim-jaemin420/%EC%9B%B9%ED%8C%A9%EC%9B%B9%ED%8C%A9%EC%9D%B4%EB%9E%80-%EC%9B%B9%ED%8C%A9%EC%9D%B4-%ED%95%98%EB%8A%94-%EC%9D%BC%EA%B3%BC-%ED%95%84%EC%9A%94%ED%95%9C-%EC%9D%B4%EC%9C%A0
