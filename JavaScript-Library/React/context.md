# Context

> Context를 이용하면 컴포넌트마다 props를 넘겨주지 않아도 컴포넌트 트리 전체에 데이터를 제공할 수 있습니다.

일반적인 React 애플리케이션에서 데이터는 위에서 아래로 (즉, 부모로부터 자식으로) props를 통해 전달되지만, 애플리케이션 안의 여러 컴포넌트들에 전해줘야 하는 props의 경우 (예를 들면, 선호 로케일, UI 테마) 이 과정이 번거로울 수 있습니다. Context를 이용하면, 트리 단계마다 명시적으로 props를 넘겨주지 않아도 많은 컴포넌트가 데이터를 공유하도록 할 수 있습니다.

<br>

## 언제 context를 써야 할까

Context는 React 컴포넌트 트리 안에서 global data (현재 로그인한 유저, 테마, 선호하는 언어 등) 를 공유할 수 있도록 고안된 방법입니다.

예를 들어, 아래의 코드는 버튼 컴포넌트를 꾸미기 위해 theme props를 명시적으로 넘겨주고 있습니다.

```javascript
class App extends React.Component {
    render() {
        return <Toolbar theme='dark' />;
    }
}

// Toolbar 컴포넌트는 불필요한 theme props를 받아서 ThemedButton에 전달해야 합니다.
function Toolbar(props) {
    return (
        <div>
            <ThemedButton theme={props.theme} />
        </div>
    );
}

class ThemedButton extends React.Component {
    render() {
        return <Button theme={this.props.theme} />;
    }
}
```

Context를 사용하면 중간에 있는 엘리먼트들에게 props를 넘겨주지 않아도 됩니다.

```javascript
// light를 기본값으로 한느 테마 context를 만듭니다.
const ThemeContext = React.createContext("light");

// Provider를 이용해 하위 트리에 테마 값 dark를 보냅니다.
class App extends React.Component {
    render() {
        return (
            <ThemeContext.Provider value='dark'>
                <Toolbar />
            </ThemeContext.Provider>
        );
    }
}

// 이제 중간에 있는 컴포넌트가 일일이 테마를 넘겨줄 필요가 없습니다.
function Toolbar() {
    return (
        <div>
            <ThemedButton />
        </div>
    );
}

// 현재 선택된 테마 값을 읽기 위해 contextType을 지정합니다.
// React는 가장 가까이 있는 테마 Provider를 찾아 그 값을 사용할 것입니다.
class ThemedButton extends React.Component {
    static contextType = ThemeContext;
    render() {
        return <Button theme={this.context} />;
    }
}
```

<br>

## Context를 사용하기 전에 고려할 것

Context의 주된 용도는 다양한 레벨에 네스팅된 많은 컴포넌트에게 데이터를 전달하는 것입니다. Context를 사용하면 컴포넌트를 재사용하기 어려워지므로 꼭 필요할 때만 사용해야 합니다.

여러 레벨에 걸쳐 props 넘기는 걸 대체하는 데에 context보다 컴포넌트 합성이 더 간단한 해결책일 수도 있습니다.

예를 들어 여러 단계 아래에 있는 `Link`와 `Avatar` 컴포넌트에게 `user`와 `avatarSize`라는 props를 전달해야 하는 `Page` 컴포넌트를 생각해봅시다.

```javascript
<Page user={user} avatarSize={avatarSize} />
    // ... 그 아래에 ...
    <PageLayout user={user} avatarSize={avatarSize} />
        // ... 그 아래에 ...
        <NavigationBar user={user} avatarSize={avatarSize} />
            // ... 그 아래에 ...
            <Link href={user.permalink}>
                <Avatar user={user} size={avatarSize} />
            </Link>
```

실제로 사용되는 곳은 `Avatar` 컴포넌트 뿐인데 `user`와 `avatarSize` props를 여러 단계에 걸쳐 보내줘야 한다는 게 번거로워 보일 수 있습니다. 게다가 위에서 `Avatar` 컴포넌트로 보내줘야하는 props가 추가된다면 그 또한 중간 레벨에 모두 추가해줘야 합니다.

`Avatar` 컴포넌트 자체를 넘겨주면 context를 사용하지 않고 이를 해결할 수 있습니다. 그러면 중간에 있는 컴포넌트들이 `user`나 `avatarSize`에 대해 전혀 알 필요가 없습니다.

```javascript
function Page(props) {
    const user = props.user;
    const userLink = (
        <Link href={user.permalink}>
            <Avatar user={user} size={props.avatarSize} />
        </Link>
    );
    return <PageLayout userLink={userLink} />;
}

// 이제 이렇게 쓸 수 있습니다.
<Page user={user} avatarSize={avatarSize} />
    // ... 그 아래에 ...
    <PageLayout user={user} avatarSize={avatarSize} />
        // ... 그 아래에 ...
        <NavigationBar user={user} avatarSize={avatarSize} />
            // ... 그 아래에 ...
            {props.userLink}
```

이렇게 바꾸면 `Link`와 `Avatar` 컴포넌트가 `user`와 `avatarSize` props를 쓴다는 걸 알아야 하는 건 가장 위에 있는 `Page` 뿐입니다.

이러한 제어의 역전(inversion of control)을 이용하면 넘겨줘야 하는 props의 수는 줄고 최상위 컴포넌트의 제어력은 더 커지기 때문에 더 깔끔한 코드를 쓸 수 있는 경우가 많습니다. 하지만 이러한 역전이 항상 옳은 것은 아닙니다. 복잡한 로직을 상위로 옮기면 이 상위 컴포넌트들은 더 난해해지기 마련이고 하위 컴포넌트들은 필요 이상으로 유연해져야 합니다.

자식으로 둘 수 있는 컴포넌트의 수에 제한은 없습니다.

```javascript
function Page(props) {
    const user = props.user;
    const content = <Feed user={user} />;
    const topBar = (
        <NavigationBar>
            <Link href={user.premalink}>
                <Avatar user={user} size={props.avatarSize} />
            </Link>
        </NavigationBar>
    );
    return <PageLayout topBar={topBar} content={content} />;
}
```

이 패턴을 사용하면 자식 컴포넌트와 직속 부모를 분리(decouple)하는 문제는 대개 해결할 수 있습니다. 더 나아가 render props를 이용하면 렌더링 되기 전부터 자식 컴포넌트가 부모 컴포넌트와 소통하게 할 수 있습니다.

하지만 같은 데이터를 트리 안 여러 레벨에 있는 많은 컴포넌트에 주어야 할 때도 있습니다. 이런 데이터 값이 변할 때마다 모든 하위 컴포넌트에게 널리 "방송"하는 것이 context입니다. 흔히 예시로 드는 선호 로케일, 테마, 데이터 캐시 등을 관리하는 데 있어서는 일반적으로 context를 사용하는 게 가장 편리합니다.

<br>

## Reference

https://ko.reactjs.org/docs/context.html
