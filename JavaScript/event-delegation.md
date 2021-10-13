# 이벤트 위임(Event Delegation)

## 이벤트 위임의 동작

1. 컨테이너에 하나의 핸들러를 할당합니다.
2. 핸들러의 `event.target`을 사용해 이벤트가 발생한 요소가 어디인지 알아냅니다.
3. 원하는 요소에서 이벤트가 발생했다고 확인되면 이벤트를 핸들링합니다.

<br>

## 이벤트 위임의 장점

-   많은 핸들러를 할당하지 않아도 되기 때문에 초기화가 단순해지고 메모리가 절약됩니다.
-   요소를 추가하거나 제거할 때 해당 요소에 할당된 핸들러를 추가하거나 제거할 필요가 없으므로 코드가 짧아집니다.
-   `innerHTML`이나 유사한 기능을 하는 스크립트로 요소 덩어리를 더하거나 뺄 수 있으므로 DOM 수정이 쉬워집니다.

<br>

## 이벤트 위임의 단점

-   이벤트 위임을 사용하려면 반드시 버블링 되어야 합니다. 하지만 몇몇 이벤트는 버블링 되지 않습니다. 그리고 낮은 레벨에 할당한 핸들러엔 `event.stopPropagation()`을 쓸 수 없습니다.
-   컨테이너 수준에 할당된 핸들러가 응답할 필요가 있는 이벤트이든 아니든 상관없이 모든 하위 컨테이너에서 발생하는 이벤트에 응답해야 하므로 CPU 작업 과부하가 늘어날 수 있습니다. 그런데 이런 부하는 무시할만한 수준이므로 실제로는 잘 고려하지 않습니다.

<br>

## 이벤트 위임의 예시

```html
<table>
    <tr>
        <th colspan="3">
            <em>Bagua</em> Chart: Direction, Element, Color, Meaning
        </th>
    </tr>
    <tr>
        <td class="nw">
            <strong>Northwest</strong><br />Metal<br />Silver<br />Elders
        </td>
        <td class="n">...</td>
        <td class="ne">...</td>
    </tr>
    <tr>
        ...2 more lines of this kind...
    </tr>
    <tr>
        ...2 more lines of this kind...
    </tr>
</table>
```

지금 해야 할 작업은 `<td>`를 클릭했을 때, 그 칸을 강조하는 것입니다. 각 `<td>`마다 `onClick` 핸들러를 할당하는 대신, 모든 이벤트를 잡아내는 핸들러를 `<table>`에 할당해 보겠습니다. `<table>`에 할당하게 될 핸들러는 `event.target`을 이용해 어떤 요소가 클릭 되었는지 감지하고, 해당 칸을 강조하게 됩니다.

```javascript
let selectedId;

table.onClick = function (event) {
    let target = event.target; // 클릭이 발생한 요소를 찾습니다.

    if (target.tagName !== "TD") return; // TD에서 발생한 클릭이 아니라면, 아무 작업도 하지 않습니다.

    highlight(target); // 클릭한 요소를 강조합니다.
};

function highlight(td) {
    // 이미 강조되어있는 칸이 있다면, 원상태로 돌려줍니다.
    if (selectedId) {
        selectedId.classList.remove("highlight");
    }
    selectedId = td;
    selectedId.classList.add("highlight"); // 새로운 td를 강조합니다.
}
```

이렇게 코드를 작성하면 테이블 내 칸의 개수는 고민거리가 되지 않습니다. 언제라도 강조기능을 유지하면서 `<td>`를 넣고 뺄 수 있게 됩니다.

하지만 단점도 있습니다. 위와 같이 구현하면 클릭 이벤트가 `<td>`가 아닌 `<td>` 안에서 발생하면 제대로 동작하지 않습니다.

위의 HTML을 살펴보면, `<td>` 안에 중첩 태그 `<strong>`이 있는 것을 확인할 수 있습니다. `<strong>`을 클릭하면 `event.target`에 `<strong>`이 저장됩니다.

따라서 `table.onClick` 핸들러에서 `event.target`을 이용해 클릭 이벤트가 `<td>` 안에서 일어났는지 아닌지를 확인해야 합니다.

```javascript
table.onClick = function (event) {
    // elem.closest(selector) 메소드는 elem의 상위 요소 중 selector와 일치하는 가장 근접한 조상 요소를 반환합니다.
    // 아래 코드에서는 이벤트가 발생한 요소부터 시작해 위로 올라가며 가장 가까운 <td>를 찾습니다.
    let td = event.target.closest("td");

    if (!td) return;

    if (!table.contains(td)) return;

    highlight(td);
};
```

<br>

## Reference

https://ko.javascript.info/event-delegation
