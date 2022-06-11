# 이벤트 버블링(Event Bubbling)과 이벤트 캡처링(Event Capturing)

## 이벤트 버블링(Event Bubbling)

한 요소에 이벤트가 발생하면, 이 요소에 할당된 핸들러가 동작하고, 이어서 부모 요소의 핸들러가 동작합니다. 가장 최상단의 조상 요소를 만날 때까지 이 과정이 반복되면서 요소 각각에 할당된 핸들러가 동작합니다.

⚠️ **거의** 모든 이벤트는 버블링 되지만, `focus` 이벤트와 같이 버블링 되지 않는 이벤트도 있습니다.

### 예시

```html
<form onclick="alert('form')">
    FORM
    <div onclick="alert('div')">
        DIV
        <p onclick="alert('p')">P</p>
    </div>
</form>
```

가장 안쪽의 `<p>`를 클릭하면 순서대로 다음과 같은 일이 벌어집니다.

1. `<p>`에 할당된 `onclick` 핸들러가 동작합니다.
2. 바깥의 `<div>`에 할당된 핸들러가 동작합니다.
3. 그 바깥의 `<form>`에 할당된 핸들러가 동작합니다.
4. `document` 객체를 만날 때까지, 각 요소에 할당된 `onclick` 핸들러가 동작합니다.

이런 동작 방식때문에 `<p>` 요소를 클릭하면 `p` > `div` > `form` 순서로 3개의 alert창이 뜹니다.

### event.target

부모 요소의 핸들러는 `event.target`를 사용해 이벤트가 발생한 요소(타켓 요소)에 접근할 수 있습니다.

`event.target`과 `this(event.currentTarget)`은 다음과 같은 차이가 있습니다.

-   `event.target`은 실제 이벤트가 시작된 **타겟 요소**입니다. 버블링이 진행되어도 변하지 않습니다.
-   `this`는 **현재 요소**로, 현재 실행 중인 핸들러가 할당된 요소를 참조합니다.

위의 예시에서 `form.onclick` 핸들러의 `event.target`과 `this`는 다음과 같습니다.

-   `event.target`은 `<form>` 안쪽에 실제 클릭한 요소를 가리킵니다.
-   `this`는 `<form>` 요소에 있는 핸들러가 동작했기 때문에 `<form>` 요소를 가리킵니다.

### 버블링 중단하기

`event.stopPropagation()`을 사용해 버블링을 중단하도록 명령할 수 있습니다. `event.stopPropagation()`은 위쪽으로 일어나는 버블링은 막아주지만, 다른 핸들러들이 동작하는 건 막지 못합니다. 한 요소의 특정 이벤트를 처리하는 핸들러가 여러개인 상황에서, 핸들러 중 하나가 버블링을 멈추더라도 나머지 핸들러는 여전히 동작합니다.

버블링을 멈추고, 요소에 할당된 다른 핸들러의 동작도 막으려면 `event.stopImmediatePropagation()`을 사용해야 합니다. 이 메소드를 사용하면 요소에 할당된 특정 이벤트를 처리하는 핸들러 모두가 동작하지 않습니다.

단, 이벤트 버블링을 막아야 하는 경우는 거의 없습니다.

<br>

## 이벤트 캡처링(Event Capturing)

표준 DOM 이벤트에서 정의한 이벤트 흐름에는 3가지 단계가 있습니다.

1. 캡처링 단계 - 이벤트가 하위 요소로 전파되는 단계
2. 타겟 단계 - 이벤트가 실제 타겟 요소에 전달되는 단계
3. 버블링 단계 - 이벤트가 상위 요소로 전파되는 단계

공식적으로는 총 3개의 이벤트 흐름이 있지만, 이벤트가 실제 타겟 요소에 전달되는 단계인 '타겟 단계'는 별도로 처리되지 않습니다. 캡처링과 버블링 단계의 핸들러는 타겟 단계에서 트리거됩니다.

캡처링 단계에서 이벤트를 잡아내려면 `addEventListener`의 `capture` 옵션을 `true`로 설정해야 합니다.

```javascript
elem.addEventListener(..., { capture: true });
elem.addEventListener(..., true);
```

### 예시

```html
<form>
    FORM
    <div>
        DIV
        <p>P</p>
    </div>
</form>

<script>
    for (let elem of document.querySelectorAll("*")) {
        elem.addEventListener(
            "click",
            (e) => alert(`캡처링: ${elem.tagName}`),
            true
        );
        elem.addEventListener("click", (e) => alert(`버블링: ${elem.tagName}`));
    }
</script>
```

`<p>`를 클릭하면 다음과 같은 순서로 이벤트가 전달됩니다.

1. `HTML` > `BODY` > `FORM` > `DIV` (캡처링 단계, 첫 번째 리스너)
2. `p` (타겟 단계, 캡처링과 버블링 둘 다에 리스너를 설정했기 때문에 두 번 호출됩니다.)
3. `DIV` > `FORM` > `BODY` > `HTML` (버블링 단계, 두 번째 리스너)

### `event.eventPhase`

`event.eventPhase` 프로퍼티를 이용하면 반환되는 정수값에 따라 현재 발생 중인 이벤트 흐름의 단계를 알 수 있습니다. 하지만 핸들러를 통해 흐름 단계를 알 수 있기 때문에 이 프로퍼티는 자주 사용되지 않습니다.

<br>

https://ko.javascript.info/bubbling-and-capturing
