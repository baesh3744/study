# 속성(Attribute)와 프로퍼티(Property)

## DOM 프로퍼티

DOM 노드는 JavaScript 객체입니다. DOM 프로퍼티는 일반 JavaScript 객체처럼 행동하고 아래와 같은 특징을 보입니다.

-   어떤 값이든 가질 수 있습니다.
-   대·소문자를 가립니다. `elem.nodeType`은 동작하지만 `elem.NoDeType`은 동작하지 않습니다.

```javascript
// document.body에 새로운 프로퍼티 추가
document.body.myData = {
    name: "Caesar",
    title: "Imperator",
};

console.log(document.body.myData.title); // Imperator

// 메소드 추가
document.body.sayTagName = function () {
    console.log(this.tagName);
};

document.body.sayTagName(); // BODY
```

`Element.prototype` 같은 내장 프로토타입을 수정해 모든 요소 노드에서 프로퍼티/메소드를 사용하게 할 수도 있습니다.

```javascript
Element.prototype.sayHi = function () {
    console.log(`Hello, ${this.tagName}`);
};

document.documentElement.sayHi(); // Hello, HTML
document.body.sayHi(); // Hello, BODY
```

<br>

## HTML 속성

HTML 속성은 HTML 태그에서 사용되며, 아래와 같은 특징을 보입니다.

-   대·소문자를 가리지 않습니다. `id`와 `ID`는 동일합니다.
-   값은 항상 문자열입니다.

<br>

## HTML 속성 --> DOM 프로퍼티

### 표준 속성

HTML에서 태그는 복수의 속성을 가질 수 있습니다. 브라우저는 HTML을 파싱해 DOM 객체를 만들 때 HTML 표준 속성을 인식하고, 이 표준 속성을 사용해 DOM 프로퍼티를 만듭니다.

따라서 요소가 `id` 같은 표준 속성으로만 구성되어 있다면, 이에 해당하는 프로퍼티는 자연스레 만들어집니다. 하지만 표준이 아닌 속성은 프로퍼티로 전환되지 않습니다.

```html
<body id="test" something="non-standard">
    <script>
        console.log(document.body.id); // test
        console.log(document.body.something); // undefined
    </script>
</body>
```

한 요소에서는 표준인 속성이 다른 요소에서는 표준이 아닐 수도 있다는 점도 주의해야 합니다. `type`은 `<input>` 요소에서는 표준이지만, `<body>`에서는 아닙니다.

```html
<body id="body" type="...">
    <input id="input" type="text" />
    <script>
        console.log(input.type); // text
        console.log(body.type); // undefined
    </script>
</body>
```

### 비표준 속성

비표준 속성의 경우 아래의 메소드를 사용해 접근할 수 있습니다.

-   `elem.hasAttribute(name)` - 속성의 존재 여부 확인
-   `elem.getAttribute(name)` - 속성값을 가져옴
-   `elem.setAttribute(name, value)` - 속성값을 변경함
-   `elem.removeAttribute(name)` - 속성값을 지움

위 메소드들은 HTML에서 명시한 속성을 대상으로 동작합니다.

`elem.attributes`를 사용하면 모든 속성값을 읽을 수도 있습니다. `elem.attributes`를 호출하면 내장 클래스 Attr을 구현한 객체들을 담은 컬렉션이 반환되는데, 객체에는 `name`과 `value` 프로퍼티가 존재합니다.

```html
<body>
    <div id="elem" about="Elephant"></div>

    <script>
        console.log(elem.getAttribute("About")); // (1). Elephant

        elem.setAttribute("Test", 123); // (2)

        console.log(elem.outerHTML); // <div id="elem" about="Elephant" test="123"></div>

        for (let attr of elem.attributes) {
            console.log(`${attr.name} = ${attr.value}`); // (3)
        }
    </script>
</body>
```

1. `getAttribute("About")` - 첫 번째 글자가 대문자 A이지만, HTML 안에서는 모두 소문자가 됩니다.
2. 어떤 값이든 속성에 대입할 수 있지만, 최종적으로는 문자열로 바뀝니다.
3. `attributes`가 반환하는 컬렉션은 iterable합니다. 컬렉션에 담긴 각 객체의 `name`, `value` 프로퍼티를 사용하면 속성 전체에 접근할 수 있습니다.

<br>

## 프로퍼티-속성 동기화

표준 속성이 변하면 대응하는 프로퍼티는 자동으로 갱신됩니다. 몇몇 경우를 제외하고 프로퍼티가 변하면 속성 역시 갱신됩니다.

```html
<input />

<script>
    let input = document.querySelector("input");

    // 속성 추가 => 프로퍼티 갱신
    input.setAttribute("id", "id");
    console.log(input.id); // id

    // 프로퍼티 변경 => 속성 갱신
    input.id = "newId";
    console.log(input.getAttribute("id")); // newId

    // input.value - 동기화가 속성에서 프로퍼티 방향으로만 일어남
    // 속성 추가 => 프로퍼티 갱신
    input.setAttribute("value", "text");
    console.log(input.value); // text

    // 프로퍼티를 변경해도 속성이 갱신되지 않음
    input.value = "newValue";
    console.log(input.getAttribute("value")); // text
</script>
```

이런 기능은 유용하게 사용될 수도 있습니다. 유저의 어떤 행동 때문에 `value`가 수정되었는데 수정 전의 "원래"값으로 복구하고 싶은 경우, 속성에 저장된 값을 가지고 오면 되기 때문입니다.

<br>

## DOM 프로퍼티 값의 타입

DOM 프로퍼티는 항상 문자열인 것은 아닙니다.

체크 박스에 사용되는 `input.checked` 프로퍼티는 boolean 값을 가집니다.

```html
<input id="input" type="checkbox" checked />

<script>
    console.log(input.getAttribute("checked")); // 속성 값: 빈 문자열
    console.log(input.checked); // 프로퍼티 값: true
</script>
```

`style` 속성의 경우 문자열이지만, `style` 프로퍼티는 객체입니다.

```html
<div id="div" style="color: red; font-size: 120%"></div>

<script>
    // 속성 값: string
    console.log(div.getAttribute("style")); // color: red; font-size: 120%

    // 프로퍼티 값: object
    console.log(div.style); // [object CSSStyleDeclaration]
    console.log(div.style.color); // red
</script>
```

아주 드물긴 하지만, DOM 프로퍼티 값이 문자열이더라도 속성값과 다른 경우도 있습니다. `href` 속성이 상대 URL이나 `#hash`이더라도 `href` DOM 프로퍼티에는 항상 URL 전체가 저장되는 경우가 대표적인 예입니다.

```html
<a id="a" href="#hello">link</a>

<script>
    // 속성 값
    console.log(a.getAttribute("href")); // #hello

    // 프로퍼티 값
    console.log(a.href); // http://site.com/page#hello
</script>
```

<br>

## 비표준 속성과 dataset

### 비표준 속성을 사용하는 이유

비표준 속성은 사용자가 직접 지정한 데이터를 HTML에서 JavaScript로 넘기고 싶은 경우나 JavaScript를 사용해 조작할 HTML 요소를 표시하기 위해 사용할 수 있습니다.

```html
<div show-info="name"></div>
<div show-info="age"></div>

<script>
    // 표시한 요소를 찾고, 그 자리에 원하는 정보를 보여주는 코드
    let user = {
        name: "Pete",
        age: 25,
    };

    for (let div of document.querySelectorAll("[show-info]")) {
        let field = div.getAttribute("show-info");
        div.innerHTML = user[field];
    }
</script>
```

비표준 속성은 요소에 스타일을 적용할 때 사용되기도 합니다.

아래 예시에서는 주문 상태를 나타내는 커스텀 속성 `order-state`를 사용해 주문 상태에 따라 스타일을 변경합니다.

```html
<style>
    .order[order-state="new"] {
        color: green;
    }

    .order[order-state="pending"] {
        color: blue;
    }

    .order[order-state="canceled"] {
        color: red;
    }
</style>

<div class="order" order-state="new">A new order.</div>

<div class="order" order-state="pending">A pending order.</div>

<div class="order" order-state="canceled">A canceled order.</div>
```

이렇게 커스텀 속성을 사용하는게 `.order-state-new`, `.order-state-pending` 같은 클래스를 사용하는 것보다 선호되는 이유는 뭘까요?

이유는 속성은 클래스보다 다루기 편리하다는 점 때문입니다. 속성의 상태는 아래와 같이 쉽게 변경할 수 있습니다.

```javascript
div.setAttribute("order-state", "canceled");
```

### dataset

비표준 속성을 사용해 코드를 작성했는데 나중에 그 속성이 표준으로 등록되게 되면 문제가 발생합니다.

이런 충돌 상황을 방지하기 위해 `data-*` 속성이 있습니다.

"data-"로 시작하는 속성 전체는 개발자가 용도에 맞게 사용하도록 별도로 예약됩니다. `dataset` 프로퍼티를 사용하면 이 속성에 접근할 수 있습니다.

```html
<body data-about="Elephants">
    <script>
        console.log(document.body.dataset.about); // Elephants
    </script>
</body>
```

`data-order-state` 같이 여러 단어로 구성된 속성은 카멜 표기법을 사용해 `dataset.orderState`로 변환됩니다.

`data-*` 속성은 커스텀 데이터를 안전하고 유효하게 전달해줍니다.

<br>

## Reference

https://ko.javascript.info/dom-attributes-and-properties
