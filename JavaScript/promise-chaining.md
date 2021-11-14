# Promise Chaining

Promise chaining은 아래와 같이 생겼습니다.

```javascript
new Promise(function (resolve, reject) {
    setTimeout(() => resolve(1), 1000); // (*)
})
    .then(function (result) {
        // (**)
        console.log(result); // 1
        return 2 * result;
    })
    .then(function (result) {
        // (***)
        console.log(result); // 2
        return 2 * result;
    })
    .then(function (result) {
        console.log(result); // 4
        return 2 * result;
    });
```

Promise chaining은 `result`가 `.then()` 핸들러의 체인(사슬)을 통해 전달된다는 점에서 착안한 아이디어입니다.

위 예시는 아래와 같은 순서로 실행됩니다.

1. 1초 후 최초 promise가 실행됩니다. - `(*)`
2. 이후 첫 번째 `.then()` 핸들러가 호출됩니다. - `(**)`
3. 2에서 반환한 값은 다음 `.then()` 핸들러에 전달됩니다. - `(***)`
4. 이런 과정이 계속 이어집니다.

Promise chaining이 가능한 이유는 `promise.then()`을 호출하면 promise가 반환되기 때문에 반환된 promise에는 당연히 `.then()`을 호출할 수 있습니다.

한편 핸들러가 값을 반환할 때는 이 값이 promise의 `result`가 됩니다. 따라서 다음 `.then()`은 이 값을 이용해 호출됩니다.

### 주의

초보자는 promise 하나에 `.then()`을 여러 개 추가한 후, 이를 chaining이라고 착각하는 경우가 있습니다. 하지만 이는 chaining이 아닙니다.

```javascript
let promise = new Promise(function (resolve, reject) {
    setTimeout(() => resolve(1), 1000);
});

promise.then(function (result) {
    console.log(result); // 1
    return 2 * result;
});

promise.then(function (result) {
    console.log(result); // 1
    return 2 * result;
});

promise.then(function (result) {
    console.log(result); // 1
    return 2 * result;
});
```

위 예시에서 promise는 하나인데 여기에 등록된 핸들러는 여러 개입니다. 이 핸들러들은 `result`를 순차적으로 전달하지 않고 독립적으로 처리합니다.

<br>

## Promise 반환하기

`.then(handler)`에 사용된 핸들러가 promise를 생성하거나 반환하는 경우도 있습니다. 이 경우 이어지는 핸들러는 promise가 처리될 때까지 기다리다가 처리가 완료되면 그 결과를 받습니다.

```javascript
new Promise(function (resolve, reject) {
    setTimeout(() => resolve(1), 1000);
})
    .then(function (result) {
        console.log(result); // 1
        return new Promise(function (resolve, reject) {
            setTimeout(() => resolve(2 * result), 1000);
        });
    })
    .then(function (result) {
        console.log(result); // 2
        return new Promise(function (resolve, reject) {
            setTimeout(() => resolve(2 * result), 1000);
        });
    })
    .then(function (result) {
        console.log(result); // 4
        return 2 * result;
    });
```

위 예시도 이전 예시와 동일하기 1, 2, 4가 차례대로 출력됩니다. 다만 출력 사이에 1초의 딜레이가 생깁니다.

<br>

## loadScript 예시 개선하기

```javascript
loadScript("/article/promise-chaining/one.js")
    .then(function (script) {
        return loadScript("/article/promise-chaining/two.js");
    })
    .then(function (script) {
        return loadScript("/article/promise-chaining/three.js");
    })
    .then(function (script) {
        // ...
    });

// 화살표 함수를 이용
loadScript("/article/promise-chaining/one.js")
    .then((script) => loadScript("/article/promise-chaining/two.js"))
    .then((script) => loadScript("/article/promise-chaining/three.js"))
    .then((script) => {
        // ...
    });

// 중첩 함수를 이용
// (장점) 중첩 함수에서는 외부 스코프에 접근할 수 있음
// (단점) 멸망의 피라미드가 발생
loadScript("/article/promise-chaining/one.js").then((script1) => {
    loadScript("/article/promise-chaining/two.js").then((script2) => {
        loadScript("/article/promise-chaining/three.js").then((script3) => {
            // ...
        });
    });
});
```

<br>

## `fetch()`와 Chaining 함께 응용하기

```javascript
let promise = fetch(url);
```

위 코드를 실행하면 `url`에 네트워크 요청을 보내고 promise를 반환합니다. 원격 서버가 헤더와 함께 응답을 보내면, promise는 `response` 객체와 함께 이행됩니다. 그런데 이때 `response` 전체가 완전히 다운로드되기 전에 promise는 이행 상태가 되어버립니다.

응답이 완전히 종료되고, 응답 전체를 읽으려면 `response.text()` 메소드를 호출해야 합니다. `response.text()`는 원격 서버에서 전송한 텍스트 전체가 다운로드되면, 이 텍스트를 `result` 값으로 갖는 promise를 반환합니다.

아래 코드를 실행하면 `user.json`에 요청이 가고 서버에서 해당 텍스트를 불러옵니다.

```javascript
fetch("/article/promise-chaining/user.json")
    .then(function (response) {
        // response.text()는 응답 텍스트 전체가 다운로드되면
        // 응답 텍스트를 새로운 promise로 만들고, 이를 반환합니다.
        return response.text();
    })
    .then(function (text) {
        console.log(text); // { "name": "iliakan", "isAdmin": true }
    });
```

그런데 `response.json()` 메소드를 쓰면 원격에서 받아온 데이터를 읽고 JSON으로 파싱할 수 있습니다. 예시엔 이 메소드가 더 적합하므로 기존에 작성한 코드를 약간 변경해 보겠습니다.

```javascript
fetch("/article/promise-chaining/user.json")
    .then((response) => response.json())
    .then((user) => console.log(user.name)); // "iliakan"
```

아래 코드는 GitHub에 요청을 보내 사용자 프로필을 불러오고 아바타를 출력합니다.

```javascript
fetch("/article/promise-chaining/user.json")
    // 응답받은 내용을 json으로 불러옵니다.
    .then((response) => response.json())
    // GitHub에 요청을 보냅니다.
    .then((user) => fetch(`https://api.github.com/users/${user.name}`))
    // 응답받은 내용을 json으로 불러옵니다.
    .then((response) => response.json())
    // 3초간 아바타 이미지(githubUser.avatar_url)을 보여줍니다.
    .then((githubUser) => {
        let img = document.createElement("img");
        img.src = githubUser.avatar_url;
        img.className = "promise-avatar-example";
        document.body.append(img);

        setTimeout(() => img.remove(), 3000); // (*)
    });
```

코드는 주석에 적은 대로 잘 동작합니다. 그런데 위 코드에는 promise를 다루는데 서툰 개발자가 자주 저지르는 잠재적 문제가 내재되어 있습니다.

`(*)`로 표시한 줄을 봅시다. 만약 아바타가 잠깐 보였다가 사라진 이후에 무언가를 하고 싶으면 어떻게 해야 할까요? 사용자 정보를 수정할 수 있게 해주는 폼을 보여주는 것 같은 작업을 추가하는 경우같이 말입니다. 지금으로선 방법이 없습니다.

체인을 확장할 수 있도록 만들려면 아래와 같이 아바타가 사라질 때 이행 promise를 반환해줘야 합니다.

```javascript
fetch("/article/promise-chaining/user.json")
    .then((response) => response.json())
    .then((user) => fetch(`https://api.github.com/users/${user.name}`))
    .then((response) => response.json())
    .then(
        (githubUser) =>
            new Promise(function (resolve, reject) {
                let img = document.createElement("img");
                img.src = githubUser.avatar_url;
                img.className = "promise-avatar-example";
                document.body.append(img);

                setTimeout(() => {
                    img.remove();
                    resolve(githubUser);
                }, 3000);
            })
    )
    // 3초 후 동작합니다.
    .then((gitubUser) => console.log(`Finished showing ${githubUser.name}`));
```

비동기 동작은 항상 promise를 반환하도록 하는 것이 좋습니다. 지금은 체인을 확장할 계획이 없더라도 이렇게 구현해 놓으면 나중에 체인 확장이 필요한 경우 손쉽게 체인을 확장할 수 있습니다.

마지막으로 코드를 재사용 가능한 함수 단위로 분리해 마무리할 수 있습니다.

```javascript
function loadJson(url) {
    return fetch(url).then((response) => response.json());
}

function loadGithubUser(name) {
    return fetch(`https://api.github.com/users/${user.name}`).then((response) =>
        response.json()
    );
}

function showAvatar(githubUser) {
    return new Promise(function (resolve, reject) {
        let img = document.createElement("img");
        img.src = githubUser.avatar_url;
        img.className = "promise-avatar-example";
        document.body.append(img);

        setTimeout(() => {
            img.remove();
            resolve(githubUser);
        }, 3000);
    });
}

loadJson("/article/promise-chaining/user.json")
    .then((user) => loadGithubUser(user.name))
    .then(showAvatar)
    .then((gitubUser) => console.log(`Finished showing ${githubUser.name}`));
```

<br>

## Reference

https://ko.javascript.info/promise-chaining
