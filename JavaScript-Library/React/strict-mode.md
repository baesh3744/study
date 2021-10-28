# Strict Mode

`StrictMode`는 애플리케이션 내의 잠재적인 문제를 알아내기 위한 도구입니다. `Fragment`와 같이 UI를 렌더링하지 않으며, 자손들에 대한 부가적인 검사와 경고를 활성화합니다.

⚠️ Strict mode는 개발 모드에서만 활성화되기 때문에, 프로덕션 빌드에는 영향을 끼치지 않습니다.

아래와 같이 애플리케이션 내부 어디서든지 strict mode를 활성화할 수 있습니다.

```javascript
import React from "react";

function ExampleApplication() {
    return (
        <div>
            <Header />
            <React.StrictMode>
                <div>
                    <ComponentOne />
                    <ComponentTwo />
                </div>
            </React.StrictMode>
            <Footer />
        </div>
    );
}
```

위 예시에서 `Header`와 `Footer` 컴포넌트는 strict mode 검사가 이루어지지 않습니다. 하지만, `ComponentOne`과 `ComponentTwo`는 각각의 자손까지 검사가 이루어집니다.

<br>

## `StrictMode` 기능

### 1. 안전하지 않은 생명주기를 사용하는 컴포넌트 발견

비동기 React 애플리케이션에서 특정 생명주기 메소드들은 안전하지 않습니다. 하지만 애플리케이션이 제3의 라이브러리를 사용한다면, 해당 생명주기 메소드가 사용되지 않는다고 장담하기 어렵습니다.

Strict mode가 활성화되면, React는 안전하지 않는 생명주기 메소드를 사용한느 모든 클래스 컴포넌트 목록을 정리해 컴포넌트에 대한 정보가 담긴 경고 로그를 출력합니다.

Strict mode에 의해 발견된 문제들을 해결한다면, 향후 릴리즈되는 React에서 concurrent rendering의 이점을 얻을 수 있을 것입니다.

### 2. 레거시 문자열 ref 사용에 대한 경고

이전의 React에서 ref를 관리하는 2가지 방법 (레거시 문자열 ref API와 callback API) 을 제공하였습니다. 문자열 ref가 사용하기 더 편리하지만 몇몇 단점들이 있었습니다. 그래서 공식적으로는 callback 형태를 사용하는 것을 권장하였습니다.

React 16.3에서는 여러 단점 없는 문자열 ref의 편리함을 제공하는 세 번째 방법을 추가하였습니다.

```javascript
class MyComponent extends React.Component {
    constructor(props) {
        super(props);

        this.inputRef = React.createRef();
    }

    render() {
        return <input type='text' ref={this.inputRef} />;
    }

    componentDidMount() {
        this.inputRef.current.focus();
    }
}
```

이제는 객체 ref가 문자열 ref를 교체하는 용도로 더해졌기 때문에, strict mode는 문자열 ref의 사용에 대해 경고합니다.

### 3. 권장되지 않는 findDOMNode 사용에 대한 경고

이전의 React에서 주어진 클래스 인스턴스를 바탕으로 트리를 탐색해 DOM 노드를 찾을 수 있는 `findDOMNode`를 지원하였습니다. 일반적으로 DOM 노드에 바로 ref를 지정할 수 있기 때문에 지금은 필요하지 않습니다.

`findDOMNode`는 클래스 컴포넌트에서도 사용할 수 있었지만, 부모가 특정 자식이 렌더링되는 것을 요구하는 상황이 허용되어, 추상화 레벨이 무너지게 되었습니다. 이로 인해 부모가 자식의 DOM 노드에까지 닿을 가능성이 있어 컴포넌트의 세세한 구현을 변경할 수 없게 되어 리팩토링이 어려워지는 상황을 만들고 말았습니다. `findDOMNode`는 항상 첫 번쨰 자식을 반환하지만, Fragment와 함께 사용할 경우 컴포넌트에서 여러 DOM 노드를 렌더링하게 됩니다. `findDOMNode`는 일회성, 읽기 전용 API로, 요청이 있을 때만 값을 반환합니다. 자식 컴포넌트가 다른 노드를 렌더링하여도 `findDOMNode`는 변경 사항에 대응할 방법이 없습니다. 그러므로, `findDOMNode`는 항상 변하지 않는, 단일 DOM 노드를 반환하는 컴포넌트에서만 정상적으로 작동해왔습니다.

Ref forwarding을 사용해 커스텀 컴포넌트에 ref를 넘겨 DOM까지 닿게 하는 것으로, 이를 분명하게 만들 수 있습니다.

DOM 노드를 감싸는 wrapper를 만들어 ref를 바로 붙이는 것 역시 가능합니다.

```javascript
class MyComponent extends React.Component {
    constructor(props) {
        super(props);
        this.wrapper = React.createRef();
    }
    render() {
        return <div ref={this.wrapper}>{this.props.children}</div>;
    }
}
```

### 4. 예상치 못한 부작용 검사

개념적으로 React는 두 단계로 동작합니다.

1. 렌더링 단계는 특정 환경 (ex. DOM) 에 어떤 변화가 필요한지 결정하는 단계입니다. 이 과정에서 React는 `render`를 호출하여 이전 `render`의 결과값과 비교합니다.
2. 커밋 단계는 React가 변경 사항을 반영하는 단계입니다. (React DOM의 경우, React가 DOM 노드를 추가, 변경 및 제거합니다.) 이 단계에서 React는 `componentDidMount`나 `componentDidUpdate`와 같은 생명주기 메소드를 호출합니다.

일반적으로 커밋 단계는 매우 빠르지만, 렌더링 단계는 느릴 수 있습니다. 이로 인해, 곧 추가될 concurrent mode는 렌더링 작업을 더 작은 단위로 나누고, 작업을 중지했다 재개하는 방식으로 브라우저가 멈추는 것을 피합니다. 즉, React는 커밋하기 전에 렌더링 단계의 생명주기 메소드를 여러 번 호출하거나 에러 혹은 우선순위에 따른 작업 중단으로 아예 커밋을 하지 않을 수도 있습니다.

렌더링 단계의 생명주기 메소드들은 여러 번 호출될 수 있기 때문에, 부작용을 포함하지 않는 것이 중요합니다. 이 규칙을 무시할 경우, 메모리 누수 혹은 잘못된 애플리케이션 상태 등 다양한 문제를 일으킬 가능성이 있습니다. 불행히도 이런 문제들은 예측한 대로 동작하지 않기 때문에 발견하는 것이 어려울 수 있습니다.

Strict mode가 자동으로 부작용을 찾아주는 것은 불가능합니다. 하지만, 조금 더 예측할 수 있게끔 만들어서 문제가 되는 부분을 발견할 수 있게 도와줍니다. 이는 아래의 함수를 의도적으로 이중으로 호출하여 찾을 수 있습니다.

-   클래스 컴포넌트의 `constructor`, `render`, `shouldComponentUpdate`
-   클래스 컴포넌트의 static `getDerivedStateFromProps`
-   함수 컴포넌트의 바디
-   State updater function (`setState`의 첫 번째 인자)
-   `useState`, `useMemo`, `useReducer`에 전달되는 함수

⚠️ 생명주기 메소드들은 개발모드에서만 이중으로 호출되고, 프로덕션 모드에서는 이중으로 호출되지 않습니다.

```javascript
class TopLevelRoute extends React.Component {
    constructor(props) {
        super(props);

        SharedApplicationState.recordEvent("ExampleComponent");
    }
}
```

얼핏 보면 위 코드에는 문제가 없어 보입니다. 하지만, `SharedApplicationState.recordEvent`의 연산 결과가 계속 달라진다면, 이 컴포넌트를 여러 번 인스턴스 화했을 때 애플리케이션의 상태를 잘못된 방향으로 이끌 수 있습니다. 이와 같은 이해하기 어려운 버그들은 개발 중에 나타나지 않을 수도 있고, 일관성이 없어 발견하지 못할 수도 있습니다.

Strict mode는 `constructor`와 같은 메소드들을 의도적으로 두 번 호출하여 이와 같은 패턴을 쉽게 찾을 수 있도록 합니다.

### 5. 레거시 context API 검사

레거시 context API는 오류가 발생하기 쉬워 이후 릴리즈에서 삭제될 예정입니다. 모든 16.x 버전에서 여전히 돌아가지만, strict mode는 경고 메시지를 노출합니다.

<br>

## Reference

https://ko.reactjs.org/docs/strict-mode.html
