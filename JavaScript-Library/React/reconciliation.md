# 재조정(Reconciliation)

## Motivation

React를 사용하다 보면, '`render()` 함수는 React 엘리먼트 트리를 만드는 것이다'라는 생각이 들 것입니다. state나 props가 갱신되면 `render()` 함수는 새로운 React 엘리먼트 트리를 반환합니다. 이때 React는 최근 엘리먼트 트리에서 UI를 갱신하는 효과적인 방법을 알아낼 필요가 있습니다.

하나의 트리에서 다른 트리로 변환하기 위한 최소한의 연산 수를 구하는 일반적인 알고리즘들이 존재합니다. 하지만 이러한 알고리즘들도 n개의 엘리먼트가 있는 트리에 대해 O(n<sup>3</sup>)의 시간복잡도를 가집니다.

React에 이 알고리즘을 사용한다면, 1,000개의 엘리먼트를 보여주기 위해 10억 번의 비교 연산을 수행해야 합니다. React는 이러한 알고리즘 대신 시간복잡도 O(n)의 heuristic algorithm을 구현합니다. Heuristic algorithm은 아래 두 가지 가정을 기반으로 합니다.

1. 서로 다른 타입의 두 엘리먼트는 서로 다른 트리를 만들어냅니다.
2. 개발자는 key prop을 통해, 여러 렌더링 사이에서 어떤 자식 엘리먼트가 변경되지 않아도 될지 표시해 줄 수 있습니다.

<br>

## The Diffing Algorithm

두 개의 트리를 비교할 때, React는 두 엘리먼트의 루트 엘리먼트부터 비교합니다. 이후의 동작은 루트 엘리먼트의 타입에 따라 달라집니다.

### 엘리먼트의 타입이 다른 경우

두 루트 엘리먼트의 타입이 다르면, React는 이전 트리를 버리고 새로운 트리를 구축합니다. `<a>`에서 `<img>`로, `<Article>`에서 `<Comment>`로, 혹은 `<Button>`에서 `<div>`로 바뀌는 것 모두 트리 전체를 재구축하는 경우입니다.

트리를 버릴 때 이전 DOM 노드들은 모두 파괴됩니다. 컴포넌트 인스턴스는 `componentWillUnmount()`가 실행됩니다. 새로운 트리가 만들어질 때, 새로운 DOM 노드들이 DOM에 삽입됩니다. 그에 따라 컴포넌트 인스턴스는 `UNSAFE_componentWillMount()`가 실행되고 `componentDidMount()`가 이어서 실행됩니다. 이전 트리와 연관된 모든 state가 사라집니다.

루트 엘리먼트 아래의 모든 컴포넌트가 unmount되고 그 state도 사라집니다. 예를 들어, 아래와 같은 비교가 일어나면,

```javascript
<div>
    <Counter />
</div>

<span>
    <Counter />
</span>
```

이전 `Counter`는 사라지고, 다시 mount될 것입니다.

<br>

### DOM 엘리먼트의 타입이 같은 경우

같은 타입의 두 React DOM 엘리먼트를 비교할 때, React는 두 엘리먼트의 속성을 확인하여, 동일한 속성은 유지하고 변경된 속성들만 갱신합니다.

예를 들어,

```javascript
<div className='before' title='stuff' />

<div className='after' title='stuff' />
```

이 두 엘리먼트를 비교하면, 현재 DOM 노드 상에 `className`만 수정합니다.

React는 `style`을 갱신할 때도 변경된 속성만을 갱신합니다. 예를 들어,

```javascript
<div style={{ color: 'red', fontWeight: 'bold' }} />

<div style={{ color: 'green', fontWeight: 'bold' }} />
```

위 두 엘리먼트 사이에서 변경될 때, React는 `fontWeight`는 수정하지 않고 `color` 속성만 수정합니다.

DOM 노드의 처리가 끝나면, React는 이어서 해당 노드의 자식들을 재귀적으로 처리합니다.

<br>

### 같은 타입의 컴포넌트 엘리먼트

컴포넌트가 갱신되면 인스턴스는 동일하게 유지되어 렌더링 간 state가 유지됩니다. React는 새로운 엘리먼트의 내용을 반영하기 위해 현재 컴포넌트 인스턴스의 props를 갱신합니다. 이때 해당 인스턴스의 `UNSAFE_componentWillReceiveProps()`, `UNSAFE_componentWillUpdate()`, `componentDidUpdate()`를 호출합니다.

다음으로 `render()` 메소드가 호출되고 비교 알고리즘이 이전 결과와 새로운 결과를 재귀적으로 처리합니다.

<br>

### 자식에 대한 재귀적 처리

DOM 노드의 자식들을 재귀적으로 처리할 때, React는 기본적으로 동시에 두 리스트를 순회하고 차이를 갱신합니다.

예를 들어, 자식의 끝에 엘리먼트를 추가하면, 두 트리 사이의 변경은 잘 작동할 것입니다.

```javascript
<ul>
    <li>first</li>
    <li>second</li>
</ul>

<ul>
    <li>first</li>
    <li>second</li>
    <li>third</li>
</ul>
```

React는 두 트리에서 `<li>first</li>`가 일치하는 것을 확인하고, `<li>second</li>`가 일치하는 것을 확인합니다. 그리고 마지막으로 `<li>third</li>`를 트리에 추가합니다.

하지만 위와 같이 단순하게 구현하면, 리스트의 맨 앞에 엘리먼트를 추가하는 경우에 성능이 좋지 않습니다. 예를 들어,

```javascript
<ul>
    <li>Duke</li>
    <li>Villanova</li>
</ul>

<ul>
    <li>Connecticut</li>
    <li>Duke</li>
    <li>Villanova</li>
</ul>
```

React는 `<li>Duke</li>`와 `<li>Villanova</li>` 트리를 그대로 유지하지 않고 모든 자식을 변경합니다.

<br>

### Keys

이러한 문제를 해결하기 위해 React는 `key` 속성을 지원합니다. 자식들이 `key`를 가지고 있다면, React는 `key`를 통해 기존 트리와 이후 트리의 자식들이 일치하는지 확인합니다.

```javascript
<ul>
    <li key='2015'>Duke</li>
    <li key='2016'>Villanova</li>
</ul>

<ul>
    <li key='2014'>Connecticut</li>
    <li key='2015'>Duke</li>
    <li key='2016'>Villanova</li>
</ul>
```

이제 React는 `'2014' key`를 가진 엘리먼트가 추가되었고, `'2015' key`와 `'2016' key`를 가진 엘리먼트는 이동만 하면 되는 것을 알 수 있습니다.

`key`로 사용할 값을 정하는 것은 어렵지 않습니다. 그리려고 하는 엘리먼트는 일반적으로 식별자를 가지고 있을 것이고, 그대로 해당 데이터를 `key`로 사용할 수 있습니다.

이러한 상황에 해당하지 않는다면, 데이터 구조에 ID라는 속성을 추가해주거나 데이터 일부에 해시를 적용해서 `key`를 생성할 수 있습니다. 해당 `key`는 형제 엘리먼트 사이에서만 유일하면 되고, 전역에서 유일할 필요는 없습니다.

최후의 수단으로 배열의 인덱스를 `key`로 사용할 수 있습니다. 항목들이 재배열되지 않는다면 이 방법도 잘 동작할 것이지만, 재배열되는 경우 비효율적으로 동작할 것입니다.

인덱스를 `key`로 사용 중 배열이 재배열되면 컴포넌트의 state와 관련된 문제가 발생할 수 있습니다. 컴포넌트 인스턴스는 `key`를 기반으로 갱신되고 재사용됩니다. 인덱스를 `key`로 사용하면, 항목의 순서가 바뀌었을 때 `key` 또한 바뀔 것입니다. 그 결과, 컴포넌트의 state가 엉망이 되거나 의도하지 않은 방식으로 바뀔 수도 있습니다.

<br>

## Reference

https://ko.reactjs.org/docs/reconciliation.html
