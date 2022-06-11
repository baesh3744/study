# 고차 함수(HOF, Higher-Order Function)

고차 함수는 함수를 인자로 전달받거나 결과로 반환하는 함수를 말합니다. JavaScript의 함수는 일급 객체이므로 함수를 값처럼 인자로 전달하거나 결과로 반환할 수 있습니다.

고차 함수는 외부 상태의 변경이나 가변 데이터를 피하고 불변성을 지향하는 함수형 프로그래밍에 기반을 두고 있습니다.

대부분의 고차 함수는 파라미터로 콜백 함수를 받아 사용되기 때문에 원본 배열을 바탕으로 하는 새로운 결과값을 창조하는데 사용됩니다.

```javascript
Array.prototype.forEach();
Array.prototype.map();
Array.prototype.filter();
Array.prototype.reduce();
...
```

<br>

## Reference

-   [junh0328/prepare_frontend_interview - "고차 함수에 대해서 아나요?"](https://github.com/junh0328/prepare_frontend_interview/blob/main/JS.md#%EA%B3%A0%EC%B0%A8-%ED%95%A8%EC%88%98%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EC%95%84%EB%82%98%EC%9A%94)
