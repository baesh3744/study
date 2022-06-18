# Today I Learned

## 알고리즘

- 그래프
  - 유향 그래프
    - [강한 결합 요소(SCC, Strongly Connected Component)](algorithm/graph/directed-graph/strongly-connected-component.md)
    - [이분 매칭(Bipartite Matching)](algorithm/graph/directed-graph/bipartite-matching.md)
    - [호프크로프트-카프 알고리즘(Hopcroft-Karp Algorithm)](algorithm/graph/directed-graph/hopcroft-karp-algorithm.md)
    - [위상 정렬(Topology Sort)](algorithm/graph/directed-graph/topology-sort.md)
    - [최대 유량 알고리즘(Maximum Flow Algorithm)](algorithm/graph/directed-graph/maximum-flow-algorithm.md)
  - 무향 그래프
    - [단절점(Articulation Point)과 단절선(Articulation Bridge)](algorithm/graph/undirected-graph/articulation-point-and-bridge.md)
  - 최단 경로 알고리즘
    - [다익스트라(Dijkstra)](algorithm/graph/shortest-path-algorithm/dijkstra.md)
    - [벨만-포드 알고리즘(Bellman-Ford Algorithm)](algorithm/graph/shortest-path-algorithm/bellman-ford-algorithm.md)
    - [플로이드-와샬(Floyd-Warshall)](algorithm/graph/shortest-path-algorithm/floyd-warshall.md)
    - [SPFA(Shortest Path Faster Algorithm)](algorithm/graph/shortest-path-algorithm/spfa.md)
- 문자열
  - [KMP(Knuth, Morris, Pratt)](algorithm/string/kmp.md)
  - [접미사 배열(Suffix Array)과 LCP 배열](algorithm/string/suffix-array-and-lcp-array.md)
- 기하학
  - [볼록 껍질(Convex Hull)](algorithm/geometry/convex-hull.md)
- 트리
  - [최소 공통 조상(LCA, Lowest Common Ancestor)](algorithm/tree/lowest-common-ancestor.md)
  - [트리의 지름(Diameter of tree)](algorithm/tree/diameter-of-tree.md)
- DP
  - [배낭 문제(Knapsack Problem)](algorithm/dp/knapsack-problem.md)
  - [최장 공통 부분수열 (LCS. Longest Common Subsequence)](algorithm/dp/longest-common-subsequence.md)
  - [최장 증가 부분수열(LIS, Longest Increasing Subsequence)](algorithm/dp/longest-increasing-subsequence.md)
- 기타
  - [수학 지식을 이용한 알고리즘들](algorithm/etc/math.md)
  - [Union-Find](algorithm/etc/union-find.md)

## 자료 구조

- [세그먼트 트리(Segment Tree)](data-structure/segment-tree.md)
  - [느리게 갱신되는 세그먼트 트리](data-structure/segment-tree-and-lazy-propagation.md)
  - [머지 소트 트리(Merge Sort Tree)](data-structure/merge-sort-tree.md)
  - [펜윅 트리(Fenwick Tree)](data-structure/fenwick-tree.md)
- [이진 트리(Binary Tree)](data-structure/binary-tree.md)
- [최소 스패닝 트리(MST, Minimum Spanning Tree)](data-structure/minimum-spanning-tree.md)
- [트라이(Trie)](data-structure/trie.md)

## CS

- [객체 지향 프로그래밍](cs/oop.md)
- [함수형 프로그래밍(Functional Programming)](cs/functional-programming.md)
- [컴파일러(Compiler)와 인터프리터(Interpreter)](cs/compiler-interpreter.md)
- [정적 타이핑(Static Typing)과 동적 타이핑(Dynamic Typing)](cs/static-dynamic-typing.md)

## OS

- [프로세스와 스레드의 차이](os/process-vs-thread.md)

## Python

- Class
  - [collections.Counter](python/class/collections.Counter.md)
- Module
  - [bisect](python/module/bisect.md)
- [거듭제곱](python/pow.md)
- [배열 -> 문자열](python/list-to-string.md)
- [얕은 복사(Shallow Copy) vs. 깊은 복사(Deep Copy)](python/copy.md)
- [입력 잡기술](python/input.md)

## HTML

- [쿼크 모드(Quirks Mode)와 표준 모드(Standards Mode)](html/quirks-mode-and-standards-mode.md)
- [`data-*` 속성](html/data-attribute.md)
- [DOCTYPE](html/doctype.md)
- [HTML/XML/XHTML](html/html-xml-xhtml.md)

## CSS

- [position](css/position.md)

## JavaScript

### [Interview Questions](javascript/interview-questions.md)

- 기본
  - [엄격 모드(Strict Mode)](javascript/fundamental/strict-mode.md)
  - [호이스팅(Hoisting)](javascript/fundamental/hoisting.md)
  - [`const` / `let` / `var`](javascript/fundamental/const-let-var.md)
  - [null / undefined / undeclared](javascript/fundamental/null-undefined-undeclared.md)
  - [데이터 타입](javascript/fundamental/data-type.md)
  - [타입 변환](javascript/fundamental/type-conversion.md)
  - [Truthy/Falsy](javascript/fundamental/truthy-falsy.md)
  - [배열(Array)](javascript/fundamental/array.md)
  - [밀집 배열(Dense Array)와 희소 배열(Sparse Array)](javascript/fundamental/dense-sparse-array.md)
  - [스코프(Scope)](javascript/fundamental/scope.md)
  - [실행 컨텍스트(Execution Context)](javascript/fundamental/execution-context.md)
  - [모듈(Module)](javascript/fundamental/module.md)
- 함수
  - [함수(Function)](javascript/function/function.md)
  - [익명 함수(Anonymous Function)](javascript/function/anonymous-function.md)
  - [클로저(Closure)](javascript/function/closure.md)
  - [함수 바인딩](javascript/function/bind.md)
  - [`call()`/`apply()`와 데코레이터, 포워딩](javascript/function/call-apply-and-decorator-forwarding.md)
  - [IIFE(Immediately Invoked Function Expression)](javascript/function/iife.md)
  - [고차 함수(HOF, Higher-Order Function)](javascript/function/hof.md)
- 객체
  - [객체(Object)](javascript/object/object.md)
  - [객체의 분류](javascript/object/classification-of-object.md)
  - [1급 객체(First-Class Object)](javascript/object/first-class-object.md)
  - [new 연산자와 생성자 함수](javascript/object/new-operator-and-constructor-function.md)
  - [this](javascript/object/this.md)
  - [Symbol](javascript/object/symbol.md)
- 프로토타입
  - [상속과 프로토타입 체인](javascript/prototype/inheritance-and-prototype-chain.md)
  - [프로토타입(Prototype)](javascript/prototype/prototype.md)
  - [프로토타입 체인(Prototype Chain)](javascript/prototype/prototype-chain.md)
- 클래스
  - [클래스(Class)](javascript/class/class.md)
- 비동기
  - [콜백(Callback)](javascript/async/callback.md)
  - [Promise](javascript/async/promise.md)
  - [Promise Chaining](javascript/async/promise-chaining.md)
  - [Promise와 에러 핸들링](javascript/async/promise-and-error-handling.md)
  - [Promise API](javascript/async/promise-api.md)
  - [Promisify](javascript/async/promisify.md)
  - [마이크로태스크(Microtask)](javascript/async/microtask.md)
  - [async와 await](javascript/async/async-await.md)
- 제너레이터
  - [제너레이터(Generator)](javascript/generator/generator.md)
  - [async 이터레이터와 제너레이터](javascript/generator/async-iterator-generator.md)

## 브라우저

- 문서
  - [속성(Attribute)와 프로퍼티(Property)](browser/document/attribute-property.md)
- 이벤트
  - [이벤트 버블링(Event Bubbling)과 이벤트 캡처링(Event Capturing)](browser/event/event-bubbling-and-capturing.md)
  - [이벤트 위임(Event Delegation)](browser/event/event-delegation.md)
- 브라우저에 데이터 저장하기
  - [쿠키와 document.cookie](browser/storing-data/cookie.md)
  - [localStorage와 sessionStorage](browser/storing-data/localStorage-sessionStorage.md)

## 웹

- [검색 엔진 최적화(SEO, Search Engine Optimization)](web/seo.md)
- [CSR / SSR / PSSR](web/csr-ssr-pssr.md)
- [HTTP와 HTTPS](web/http-and-https.md)
- [HTTP Method](web/http-method.md)
- [RESTful API](web/restful-api.md)
- [SPA / MPA / PWA](web/spa-mpa-pwa.md)
- [TCP vs. UDP](web/tcp-vs-udp.md)
- [웹팩(webpack)](web/webpack.md)
- [바벨(Babel)](web/babel.md)
- [TypeScript](web/typescript.md)
- [AJAX](web/ajax.md)
- [반응형 디자인(Responsive Design) vs. 적응형 디자인(Adaptive Design)](web/responsive-design-and-adaptive-design.md)

## Gatsby

- 라우팅
  - [Creating Routes](Gatsby/routing/creating-routes.md)
  - [`<Link>`](Gatsby/routing/Link-API.md)

## React

- [재조정(Reconciliation)](React/reconciliation.md)
- [Context](React/context.md)
- [Hook](React/hook.md)
- [Portal](React/portal.md)
- [Ref와 DOM](React/ref-and-dom.md)
- [Ref 전달하기](React/forwarding-refs.md)
- [Strict Mode](React/strict-mode.md)

## 번역

<details>
<summary>React Hook Form</summary>

> https://react-hook-form.com

- [Get Started](react-hook-form/get-started.md)
- [API](react-hook-form/api/api.md)
  - [useForm](react-hook-form/api/useForm/useForm.md)
    - [register](react-hook-form/api/useForm/register.md)
    - [unregister](react-hook-form/api/useForm/unregister.md)
    - [formState](react-hook-form/api/useForm/formState.md)
    - [watch](react-hook-form/api/useForm/watch.md)
    - [handleSubmit](react-hook-form/api/useForm/handleSubmit.md)
    - [reset](react-hook-form/api/useForm/reset.md)
    - [resetField](react-hook-form/api/useForm/resetField.md)
    - [setError](react-hook-form/api/useForm/setError.md)
    - [clearErrors](react-hook-form/api/useForm/clearErrors.md)
    - [setValue](react-hook-form/api/useForm/setValue.md)
    - [setFocus](react-hook-form/api/useForm/setFocus.md)
    - [getValues](react-hook-form/api/useForm/getValues.md)
    - [getFieldState](react-hook-form/api/useForm/getFieldState.md)
    - [trigger](react-hook-form/api/useForm/trigger.md)
    - [control](react-hook-form/api/useForm/control.md)
  - [useController](react-hook-form/api/useController/useController.md)
    - [Controller](react-hook-form/api/useController/Controller.md)
  - [useFormContext](react-hook-form/api/useFormContext/useFormContext.md)
  - [useWatch](react-hook-form/api/useWatch/useWatch.md)
  - [useFormState](react-hook-form/api/useFormState/useFormState.md)
    - [ErrorMessage](react-hook-form/api/useFormState/ErrorMessage.md)
  - [useFieldArray](react-hook-form/api/useFieldArray/useFieldArray.md)

</details>

## 정리

- 정규표현식
  - [Lookaround](regex/lookaround.md)
