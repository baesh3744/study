# Ref와 DOM

Ref는 `render()` 메소드에서 생성된 DOM 노드나 React 엘리먼트에 접근하는 방법을 제공합니다.

일반적인 React의 데이터 플로우에서 props는 부모 컴포넌트가 자식과 상호작용할 수 있는 유일한 수단입니다. 자식을 수정하려면 새로운 props를 전달하여 자식을 다시 렌더링해야 합니다. 그러나 일반적인 데이터 플로우에서 벗어나 직접적으로 자식을 수정해야 하는 경우도 가끔씩 있습니다. 수정할 자식은 React 컴포넌트의 인스턴스일 수도 있고, DOM 엘리먼트일 수도 있습니다. React는 두 경우 모두를 위한 해결책을 제공합니다.

## Ref를 사용해야 할 때

Ref의 바람직한 사용 사례는 다음과 같습니다.

-   포커스, 텍스트 선택영역, 혹은 미디어의 재생을 관리할 때
-   애니메이션을 직접적으로 실행시킬 때
-   제3의 DOM 라이브러리를 React와 같이 사용할 때

선언적으로 해결될 수 있는 문제에서는 ref 사용을 지양해야 합니다. 예를 들어, `Dialog` 컴포넌트에서 `open()`과 `close()` 메소드를 두는 대신, `isOpen`이라는 prop을 넘겨주세요.

<br>

## Ref 생성하기

Ref는 `React.createRef()`를 통해 생성되고 `ref` attribute를 통해 React 엘리먼트에 부착됩니다. 보통 컴포넌트의 인스턴스가 생성될 때 Ref를 property로서 추가하고, 그럼으로써 컴포넌트의 인스턴스의 어느 곳에서도 Ref에 접근할 수 있게 합니다.

```javascript
class MyComponent extends React.Component {
    constructor(props) {
        super(props);
        this.myRef = React.createRef();
    }
    render() {
        return <div ref={this.myRef} />;
    }
}
```

<br>

## Ref에 접근하기

`render()` 메소드 안에서 ref가 엘리먼트에게 전달되었을 때, 그 노드를 향한 참조는 ref의 `current` attribute에 담기게 됩니다.

Ref의 값은 노드의 유형에 따라 다릅니다.

-   `ref` attribute가 HTML 엘리먼트에 쓰였다면, 생성자에서 `React.createRef()`로 생성된 `ref`는 자신을 전달받은 DOM 엘리먼트를 `current` property의 값으로서 받습니다.
-   `ref` attribute가 커스텀 클래스 컴포넌트에 쓰였다면, `ref` 객체는 마운트된 컴포넌트의 인스턴스를 `current` property의 값으로서 받습니다.
-   함수 컴포넌트는 인스턴스가 없기 때문에 함수 컴포넌트에 `ref` attribute를 사용할 수 없습니다.

### DOM 엘리먼트에 Ref 사용하기

```javascript
class CustomTextInput extends React.Component {
    constructor(props) {
        super(props);
        this.textInput = React.createRef();
        this.focusTextInput = this.focusTextInput.bind(this);
    }

    focusTextInput() {
        this.textInput.current.focus();
    }

    render() {
        return (
            <div>
                <input type='text' ref={this.textInput} />
                <input
                    type='button'
                    value='Focus the text input'
                    onClick={this.focusTextInput}
                />
            </div>
        );
    }
}
```

컴포넌트가 마운트될 때 React는 `current` property에 DOM 엘리먼트를 대입하고, 컴포넌트의 마운트가 해제될 때 `current` property를 다시 `null`로 돌려 놓습니다. `ref`를 수정하는 작업은 `componentDidMount()` 또는 `componentDidUpdate()` 메소드가 호출되기 전에 이루어집니다.

### 클래스 컴포넌트에 Ref 사용하기

`CustomTextInput` 컴포넌트의 인스턴스가 마운트 된 이후에 즉시 클릭되는 걸 흉내내기 위해 `CustomTextInput` 컴포넌트를 감싸는 걸 원한다면, ref를 사용하여 `CustomTextInput` 컴포넌트의 인스턴스에 접근하고 직접 `focusTextInput()` 메소드를 호출할 수 있습니다. 단, `CustomeTextInput`가 클래스 컴포넌트일 때에만 작동합니다.

```javascript
class AutoFocusTextInput extends React.Component {
    constructor(props) {
        super(props);
        this.textInput = React.createRef();
    }

    componentDidMount() {
        this.textInput.current.focusTextInput();
    }

    render() {
        return <CustomTextInput ref={this.textInput} />;
    }
}
```

### Ref와 함수 컴포넌트

함수 컴포넌트는 인스턴스가 없기 때문에 함수 컴포넌트에 `ref` attribute를 사용할 수 없습니다.

```javascript
function MyFunctionComponent() {
    return <input />;
}

class Parent extends React.Component {
    constructor(props) {
        super(props);
        this.textInput = React.createRef();
    }
    render() {
        // 이 코드는 동작하지 않습니다.
        return <MyFunctionComponent ref={this.textInput} />;
    }
}
```

함수 컴포넌트에 `ref`를 사용할 수 있도록 하려면, `forwardRef()` (높은 확률로 `useImperativeHandle()`과 함께) 를 사용하거나 클래스 컴포넌트로 변경해야 합니다.

다만, DOM 엘리먼트나 클래스 컴포넌트의 인스턴스에 접근하기 위해 `ref` attribute를 함수 컴포넌트에서 사용하는 것은 됩니다.

```javascript
function CustomTextInput(props) {
    const textInput = useRef(null);

    function handleClick() {
        textInput.current.focus();
    }

    return (
        <div>
            <input type='text' ref={textInput} />
            <input
                type='button'
                value='Focus the text input'
                onClick={handleClick}
            />
        </div>
    );
}
```

<br>

## 부모 컴포넌트에게 DOM ref를 공개하기

보기 드문 경우지만, 부모 컴포넌트에서 자식 컴포넌트의 DOM 노드에 접근하려 하는 경우도 있습니다. 자식 컴포넌트의 DOM 노드에 접근하는 것은 컴포넌트의 캡슐화를 파괴하기 때문에 권장되지 않습니다. 그렇지만 가끔 자식 컴포넌트의 DOM 노드를 포커스하는 일이나 크기 또는 위치를 계산하는 일 등을 할 때에는 효과적인 방법이 될 수 있습니다.

자식 컴포넌트에 ref를 사용할 수 있지만, 이 방법은 자식 컴포넌트의 인스턴스의 DOM 노드가 아닌 자식 컴포넌트의 인스턴스를 가져온다는 점과 자식 컴포넌트가 함수 컴포넌트인 경우에는 동작하지 않는다는 점에서 좋은 방법이 아닙니다.

React 16.3 이후 버전에서는 ref 전달하기(ref forwarding)을 사용하는 것이 권장됩니다. Ref 전달하기는 컴포넌트가 자식 컴포넌트의 ref를 자신의 ref로서 외부에 노출시키게 합니다.

가능하다면 DOM 노드를 외부에 공개하는 일을 지양해야 합니다만 DOM 노드를 외부에 공개하는 일이 유용한 해결책이 될 수 있습니다. 또한 이 방법들은 자식 컴포넌트의 코드 수정을 요합니다. 자식 컴포넌트의 코드를 수정할 수 없다면 최후의 방법인 `findDOMNode()`를 사용하는 방법이 있지만 `findDOMNode()`는 좋지 못한 방법일 뿐더러 strict mode에서 사용할 수 없습니다.

<br>

## Callback ref

React는 ref가 설정되고 해제되는 상황을 세세하게 다룰 수 있는 callback ref라 불리는 ref를 설정하기 위한 방법을 제공합니다.

Callback ref를 사용할 때에는 `ref` attribute에 `React.createRef()`를 통해 생성된 `ref`를 전달하는 대신, 함수를 전달합니다. 전달된 함수는 다른 곳에 저장되고 접근될 수 있는 React 컴포넌트의 인스턴스나 DOM 엘리먼트를 인자로서 받습니다.

```javascript
class CustomTextInput extends React.Component {
    constructor(props) {
        super(props);

        this.textInput = null;

        this.setTextInputRef = (element) => {
            this.textInput = element;
        };

        this.focusTextInput = () => {
            if (this.textInput) this.textInput.focus();
        };
    }

    componentDidMount() {
        this.focusTextInput();
    }

    render() {
        return (
            <div>
                <input type='text' ref={this.setTextInputRef} />
                <input
                    type='button'
                    value='Focus the text input'
                    onClick={this.focusTextInput}
                />
            </div>
        );
    }
}
```

컴포넌트의 인스턴스가 마운트 될 때 React는 `ref` callback을 DOM 엘리먼트와 함께 호출합니다. 그리고 컴포넌트의 인스턴스의 마운트가 해제될 때, `ref` callback을 `null`과 함께 호출합니다. `ref` callback은 `componentDidMount()` 또는 `componentDidUpdate()` 메소드가 호출되기 전에 호출됩니다.

Callback ref 또한 `React.createRef()`를 통해 생성했었던 객체 ref처럼 다른 컴포넌트에게 전달할 수 있습니다.

```javascript
function CustomTextInput(props) {
    return (
        <div>
            <input ref={props.inputRef} />
        </div>
    );
}

class Parent extends React.Component {
    render() {
        return <CustomTextInput inputRef={(el) => (this.inputElement = el)} />;
    }
}
```

위의 예시에서 `Parent`는 자신의 callback ref를 `inputRef` prop으로서 `CustomTextInput`에게 전달합니다. `CustomTextInput`은 전달받은 함수를 `<input>`에게 `ref` attribute로서 전달합니다. 결과적으로 `Parent`에 있는 `this.inputElement`는 `CustomTextInput`의 `<input>` 엘리먼트에 대응하는 DOM 노드가 됩니다.

<br>

## Reference

https://ko.reactjs.org/docs/refs-and-the-dom.html
