# `data-*` 속성

## `data-*` 속성의 장점

화면에 안 보이게 글이나 추가 정보를 엘리먼트에 담아 놓을 수 있습니다.

<br>

## `data-*` 속성의 문제점

-   Assistive technology나 검색 크롤러가 `data-*` 속성의 값에 접근할 수 없습니다. 그러므로 보여야 하고 접근가능해야 하는 정보는 `data-*` 속성에 저장하면 안됩니다.
-   JS Object에 데이터를 저장하는 것보다 `data-*` 속성에서 데이터를 읽는 것이 성능이 안 좋습니다.

<br>

## `data-*` 속성에 접근하기

### HTML

엘리먼트에 `data-`로 시작하는 속성을 추가합니다. 속성의 이름은 `data-`로 시작하는 무엇이든 될 수 있으며, 문자열 값을 가집니다.

```html
<article
    id="electriccars"
    data-columns="3"
    data-index-number="12314"
    data-parent="cars"
>
    ...
</article>
```

### JavaScript

1. `getAttribute()` 함수를 사용합니다.
2. `element.dataset.attr`을 이용해 `element`에 저장된 `data-attr` 값을 가져올 수 있습니다. (`attr`은 camelCase로 변환됩니다.)

```javascript
var article = document.getElementById("electriccars");

article.dataset.columns; // "3"
article.dataset.indexNumber; // "12314"
article.dataset.parent; // "cars"
```

### CSS

`attr()` 함수나 속성 선택자를 이용해 스타일을 바꿀 수 있습니다.

```css
article::before {
    content: attr(data-parent);
}

article[data-columns="3"] {
    width: 400px;
}
```

<br>

## Reference

https://developer.mozilla.org/ko/docs/Learn/HTML/Howto/Use_data_attributes
