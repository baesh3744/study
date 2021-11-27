# localStorage와 sessionStorage

웹 스토리지 객체(web storage object)인 `localStorage`와 `sessionStorage`는 브라우저 내에서 키-값 쌍을 저장할 수 있게 해줍니다.

이 둘을 사용하면 페이지를 새로 고침하고 (`sessionStorage`의 경우) 심지어 브라우저를 다시 실행해도 (`localStorage`의 경우) 데이터가 사라지지 않고 남아있습니다.

그런데 "쿠키를 사용하면 브라우저에 데이터를 저장할 수 있는데, 왜 또 다른 객체를 사용해 데이터를 저장하는 걸까요?"라는 의문이 들 수 있습니다. 쿠키 이외에도 다른 방식을 사용하는 이유는 다음과 같습니다.

-   쿠키와 다르게 웹 스토리지 객체는 네트워크 요청 시 서버로 전송되지 않습니다. 이런 특징 때문에 쿠키보다 더 많은 자료를 보관할 수 있습니다. 대부분의 브라우저가 최소 2MB 혹은 그 이상의 웹 스토리지 객체를 저장할 수 있도록 해줍니다. 또한 개발자는 브라우저 내 웹 스토리지 구성 방식을 설정할 수 있습니다.
-   쿠키와 또 다른 점은 서버가 HTTP 헤더를 통해 스토리지 객체를 조작할 수 없다는 것입니다. 웹 스토리지 객체 조작은 모두 JavaScript 내에서 수행됩니다.
-   웹 스토리지 객체는 도메인·프로토콜·포트로 정의되는 오리진(origin)에 묶여있습니다. 따라서 프로토콜과 서브 도메인이 다르면 데이터에 접근할 수 없습니다.

두 스토리지 객체는 동일한 메소드와 프로퍼티를 제공합니다.

-   `setItem(key, value)` - 키-값 쌍을 보관합니다.
-   `getItem(key)` - 키에 해당하는 값을 받아옵니다.
-   `removeItem(key)` - 키에 해당하는 값을 삭제합니다.
-   `clear()` - 모든 것을 삭제합니다.
-   `key(index)` - 인덱스(`index`)에 해당하는 키를 받아옵니다.
-   `length` - 저장된 항목의 개수를 얻습니다.

<br>

## `localStorage` 데모

`localStorage`의 주요 기능은 다음과 같습니다.

-   오리진이 같은 경우 데이터는 모든 탭과 창에서 공유됩니다. 오리진(domain/port/protocol)만 같다면 url 경로가 달라도 동일한 결과를 볼 수 있습니다.
-   브라우저나 OS가 재시작하더라도 데이터가 파기되지 않습니다.

<br>

### 일반 객체처럼 사용하기

`localStorage`의 키를 얻거나 설정할 때, 아래처럼 일반 객체와 유사한 방법을 사용할 수 있습니다.

```javascript
// 키 설정하기
localStorage.test = 2;

// 키 얻기
console.log(localStorage.test); // 2

// 키 삭제하기
delete localStorage.test;
```

하위 호환성 때문에 아직 이런 방법이 지원되기는 하지만, 다음과 같은 이유로 추천하지 않습니다.

1. 사용자는 `length`나 `toString`, `localStorage`의 내장 메소드를 키로 설정할 수 있습니다. 이렇게 되면 `getItem()`, `setItem()`은 정상적으로 작동해도, 일반 객체처럼 다룰 때 에러가 발생할 수 있습니다.
2. 데이터를 수정하면 `storage` 이벤트가 발생하는데, 이 이벤트는 `localStorage`를 객체처럼 접근할 땐 일어나지 않습니다.

<br>

### 키 순회하기

`localStorage`는 "키"를 사용해 값을 얻고, 설정하고, 삭제할 수 있게 해줍니다. 그렇다면 키나 값 전체는 어떻게 얻을 수 있을까요? 스토리지 객체는 iterable 객체가 아니지만, 배열처럼 다루면 전체 키-값을 얻을 수 있습니다.

```javascript
for (let i = 0; i < localStorage.length; i++) {
    let key = localStorage.key(i);
    console.log(`${key}: ${localStorage.getItem(key)}`);
}
```

일반 객체를 다룰 때처럼 `for key in localStorage` 반복문을 사용해도 전체 키-값을 얻을 수 있습니다. 하지만 이 방법을 사용하면 필요하지 않은 내장 필드까지 출력됩니다.

```javascript
// 좋지 않은 방법
for (let key in localStorage) {
    console.log(key); // getItem, setItem 같은 내장 필드까지 출력됩니다.
}
```

`for key in localStorage` 반복문을 사용하려면 `hasOwnProperty()`를 이용해 프로토타입에서 상속받은 필드를 골라내야 합니다.

```javascript
for (let key in localStorage) {
    if (!localStorage.hasOwnProperty(key)) {
        continue;
    }
    console.log(`${key}: ${localStorage.getItem(key)}`);
}
```

아니면 아래처럼 `Object.keys()`로 "자기 자신"의 키를 받아온 다음 순회하는 방법을 사용할 수도 있습니다. `Object.keys()`는 해당 객체에서 정의한 키만 반환하고 프로토타입에서 상속받은 키는 무시하기 때문입니다.

```javascript
let keys = Object.keys(localStorage);
for (let key in keys) {
    console.log(`${key}: ${localStorage.getItem(key)}`);
}
```

<br>

### 문자열만 사용

`localStorage`의 키와 값은 반드시 문자열이어야 합니다. 숫자나 객체 등 다른 자료형을 사용하게 되면 문자열로 자동 변환됩니다.

```javascript
localStorage.user = { name: "John" };
console.log(localStorage.user); // "[object Object]"
```

`JSON`을 사용하면 객체를 쓸 수 있긴 합니다.

```javascript
localStorage.user = JSON.stringify({ name: "John" });

let user = JSON.parse(localStorage.user);
console.log(user.name); // John
```

디버깅 등의 목적으로 스토리지 객체 전체를 문자열로 변환하는 것도 가능합니다.

```javascript
console.log(JSON.stringify(localStorage, null, 2));
```

<br>

## `sessionStorage`

`sessionStorage` 객체는 `localStorage`에 비해 자주 사용되지는 않습니다.

제공하는 프로퍼티와 메소드는 같지만, 훨씬 제한적이기 때문입니다.

-   `sessionStorage`는 현재 떠 있는 탭 내에서만 유지됩니다.
    -   같은 페이지라도 다른 탭에 있으면 다른 곳에 저장됩니다.
    -   하나의 탭에 여러 개의 iframe이 있는 경우에는 동일한 오리진에서 왔다고 취급되기 때문에 `sessionStorage`가 공유됩니다.
-   페이지를 새로 고침할 때 `sessionStorage`에 저장된 데이터는 사라지지 않습니다. 하지만 탭을 닫고 새로 열 때는 사라집니다.

<br>

## storage 이벤트

`localStorage`나 `sessionStorage`의 데이터가 갱신될 때, storage 이벤트가 실행됩니다. storage 이벤트는 다음과 같은 프로퍼티를 지원합니다.

-   `key` - 변경된 데이터의 키 (`.clear()`를 호출했다면 `null`)
-   `oldValue` - 이전 값 (키가 새롭게 추가되었다면 `null`)
-   `newValue` - 새로운 값 (키가 삭제되었다면 `null`)
-   `url` - 갱신이 일어난 문서의 url
-   `storageArea` - 갱신이 일어난 `localStorage`나 `sessionStorage` 객체

여기서 중요한 점은 storage 이벤트가 이벤트를 발생시킨 스토리지를 제외하고 스토리지에서 접근 가능한 `window` 객체 내부에서 일어난다는 사실입니다.

두 개의 창에 같은 사이트를 띄워놨다고 가정해봅시다. 창은 다르지만 `localStorage`는 서로 공유됩니다.

두 창에서 모두 storage 이벤트를 수신하고 있기 때문에 한 창에서 데이터를 갱신하면 다른 창에서 해당 사항이 반영되는 것을 확인할 수 있습니다.

storage 이벤트의 또 다른 중요한 특징은 `event.url`이 있어 데이터가 갱신된 문서의 url을 알 수 있다는 점입니다.

또한 `event.storageArea`에는 스토리지 객체가 포함되어 있는데, storage 이벤트는 `sessionStorage`나 `localStorage`가 변경될 때 모두 발생하기 때문에 `event.storageArea`는 스토리지 종류에 상관없이 실제 수정이 일어난 것을 참조한다는 것 역시 중요한 특징입니다. 변경이 일어났을 때 우리는 `event.storageArea`에 무언가를 설정해 "응답"이 가능하도록 할 수 있습니다.

이런 특징을 이용하면 오리진이 같은 창끼리 메시지를 교환하게 할 수 있습니다.

모던 브라우저는 오리진이 같은 창끼지 통신할 수 있도록 해주는 브로드캐스트 채널 API(broadcast channel API)를 지원합니다. 그런데 이 API는 기능은 풍부하지만, 아직 많은 곳에서 지원하지 않는다는 단점이 있습니다. 단점을 극복하게 해주는 `localStorage`에 기반한 폴리필들이 있는데, 이런 라이브러리들은 브라우저와 관계없이 어디서든 창 간 메시지를 교환할 수 있게 해준다는 장점이 있습니다.

<br>

## Reference

https://ko.javascript.info/localstorage
