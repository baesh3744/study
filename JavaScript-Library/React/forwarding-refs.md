# Ref 전달하기

Ref forwarding은 컴포넌트를 통해 자식 중 하나에 ref를 자동으로 전달하는 기법입니다. 대부분의 컴포넌트에 필요하지는 않지만, 재사용 가능한 컴포넌트 라이브러리와 같은 몇몇 컴포넌트에서는 유용할 수 있습니다.

<br>

## DOM에 refs 전달하기

기본 `button` DOM element를 렌더링하는 `FancyButton` 컴포넌트를 가정해 봅시다.

```javascript
function FancyButton(props) {
    return <button className='FancyButton'>{props.children}</button>;
}
```

`FancyButton`을 사용하는 다른 컴포넌트들은 일반적으로 내부 `button` DOM element에 대한 ref를 얻을 필요가 없습니다. 이는 컴포넌트들이 서로의 DOM 구조에 지나치게 의존하지 않기 때문에 괜찮습니다.

이런 캡슐화는 `FeedStory`나 `Comment`같은 애플리케이션 레벨의 컴포넌트에서는 바람직하지만, `FancyButton`이나 `MyTextInput`같은 재사용성이 높은 말단 요소에서는 불편할 수 있습니다. 이런 컴포넌트들은 일반적인 DOM `button`, `input`과 유사한 방법으로 애플리케이션 전체에 걸쳐 사용되는 경향이 있습니다. 그리고 포커스, 선택, 애니메이션을 관리하기 위해서는 이런 DOM 노드에 접근하는 것이 불가피할 수 있습니다.

Ref forwarding은 일부 컴포넌트가 수신한 ref를 받아 조금 더 아래로 전달할 수 있는 opt-in 기능입니다.

아래의 예에서 `FancyButton`은 `React.forwardRef`를 사용하여 전달된 `ref`를 얻고, 그것을 렌더링 되는 DOM `button`으로 전달합니다.

```javascript
// 두 번째 `ref` 인자는 컴포넌트를 `React.forwardRef`로 정의했을 때만 존재합니다.
const FancyButton = React.forwardRef((props, ref) => (
    <button ref={ref} className='FancyButton'>
        {props.children}
    </button>
));

const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

이런 방법으로 `FancyButton`을 사용하는 컴포넌트들은 `button` DOM 노드에 대한 참조를 가져올 수 있고, 필요한 경우 DOM `button`을 직접 사용하는 것처럼 접근할 수 있습니다.

위의 예시에서 어떤 일이 일어나는지 단계별로 설명하겠습니다.

1. `React.createRef`를 호출해서 React ref를 생성하고 `ref` 변수에 할당합니다.
2. `ref`를 JSX 속성으로 지정해서 `<FancyButton ref={ref}>`로 전달합니다.
3. React는 이 `ref`를 `forwardRef` 내부의 `(props, ref) => ...` 함수의 두 번째 인자로 전달합니다.
4. 이 `ref`를 JSX 속성으로 지정해서 `<button ref={ref}>`로 전달합니다.
5. ref가 첨부되면 `ref.current`는 `<button>` DOM 노드를 가리키게 됩니다.

<br>

## 고차원 컴포넌트(HOC, Higher-Order Component)에서의 ref 전달하기

```javascript
function logProps(WrappedComponent) {
    class LogProps extends React.Component {
        componentDidUpdate(prevProps) {
            console.log("old props:", prevProps);
            console.log("new props:", this.props);
        }

        render() {
            return <WrappedComponent {...this.props} />;
        }
    }

    return LogProps;
}
```

`logProps` HOC는 모든 `props`를 `WrappedComponent`로 전달하므로 `logProps`와 `WrappedComponent`의 렌더링 된 결과는 동일합니다.

이 HOC를 사용해서 `FancyButton` 컴포넌트로 전달하는 모든 `props`를 기록할 수 있습니다.

```javascript
class FancyButton extends React.Component {
    // ...
}

// FancyButton을 렌더링하는 LogProps를 export합니다.
export default logProps(FancyButton);
```

위 예시에서 한 가지 주의사항이 있습니다. Refs는 전달되지 않는다는 것입니다. 왜냐하면 `ref`는 prop이 아니기 때문입니다. `key`와 마찬가지로 `ref`는 React에서 다르게 처리합니다. HOC에 `ref`를 추가하면 `ref`는 래핑된 컴포넌트가 아니라 가장 바깥쪽 컨테이너 컴포넌트를 참조합니다.

즉, `FancyButton`을 위한 refs가 실제로는 `LogProps` 컴포넌트를 참조한다는 것을 의미합니다.

```javascript
import FancyButton from "./FancyButton";
// 가져온 FancyButton 컴포넌트는 LogProps HOC입니다.
// 렌더링된 결과가 동일하다고 하더라도,
// ref는 내부 FancyButton 컴포넌트 대신 LogProps를 가리킵니다.

const ref = React.createRef();

<FancyButton label='Click Me' handleClick={handleClick} ref={ref} />;
```

`React.forwardRef` API를 사용하여 내부 `FancyButton` 컴포넌트에 대한 refs를 명시적으로 전달할 수 있습니다. `React.forwardRef`는 `props`와 `ref` 파라미터를 받아 React 노드를 반환하는 렌더링 함수를 인자로 받습니다.

```javascript
function logProps(Component) {
    class LogProps extends React.Component {
        componentDidUpdate(prevProps) {
            console.log("old props:", prevProps);
            console.log("new props:", this.props);
        }

        render() {
            const { forwardedRef, ...rest } = this.props;

            return <Component ref={forwardedRef} {...rest} />;
        }
    }

    return React.forwardref((props, ref) => {
        return <LogProps {...props} forwardedRef={ref} />;
    });
}
```

<br>

## DevTools에 사용자 정의 이름 표시하기

`React.forwardRef`는 렌더링 함수를 받습니다. React DevTools는 이 함수를 사용하여 ref 전달 컴포넌트에 대해서 무엇을 표시할 것인지 정의합니다.

예를 들어, 아래 컴포넌트는 DevTools에 `ForwardRef`로 나타납니다.

```javascript
const WrappedComponent = React.forwardRef((props, ref) => {
    return <LogProps {...props} forwardRef={ref} />;
});
```

렌더링 함수를 지정하면 DevTools에 해당 이름도 포함되어 `ForwardRef(myFunction)`으로 나타납니다.

```javascript
const WrappedComponent = React.forwardRef(function myFunction(props, ref) {
    return <LogProps {...props} forwardRef={ref} />;
});
```

감싸고 있는 컴포넌트를 포함하도록 함수의 `displayName` 속성을 설정하여 `ForwardRef(logProps(MyComponent))`로 나타나게 할 수도 있습니다.

```javascript
function logProps(Component) {
    class LogProps extends React.Component {
        // ...
    }

    function forwardRef(props, ref) {
        return <LogProps {...props} forwardRef={ref} />;
    }

    const name = Component.displayName || Component.name;
    forwardRef.displayName = `logProps(${name})`;

    return React.forwardRef(forwardRef);
}
```

<br>

## Reference

https://ko.reactjs.org/docs/forwarding-refs.html
