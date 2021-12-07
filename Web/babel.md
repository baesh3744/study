# 바벨(Babel)

## 바벨이란

바벨은 브라우저간의 호환성을 위해 ECMAScript2015+ 코드를 하위 버전으로 변환해주는 JavaScript 컴파일러입니다.

```javascript
// Babel Input: ES2015 arrow function
[1, 2, 3].map((n) => n + 1);

// Babel Output: ES5 equivalent
[1, 2, 3].map(function (n) {
    return n + 1;
});
```

바벨이 하는 일은 아래와 같습니다.

-   Syntax 변환
-   Polyfill (기본적으로 구브라우저에서 지원하지 않는 최신 기능을 지원하는 데 필요한 코드)
-   소스 코드 변환 (codemods)
-   기타..

<br>

## Reference

https://babeljs.io/docs/en/
