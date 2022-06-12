# AJAX

## AJAX란

AJAX란 비동기 JavaScript와 XML(**A**synchronous **J**avaScript **A**nd **X**ML)을 말합니다. 간단히 말하면, 서버와 통신하기 위해 `XMLHttpRequest` 객체를 사용하는 것을 말합니다. JSON, XML, HTML 그리고 일반 텍스트 형식 등을 포함한 다양한 포맷을 주고 받을 수 있습니다. AJAX의 강력한 특징은 페이지 전체를 리프레쉬 하지 않고서도 수행 되는 "비동기성"입니다. 이러한 비동기성을 통해 사용자의 이벤트가 있으면 전체 페이지가 아닌 일부분만을 업데이트 할 수 있게 해줍니다.

AJAX의 주요 두 가지 특징은 아래의 작업을 할 수 있게 해줍니다.

-   페이지 새로고침 없이 서버에 요청
-   서버로부터 데이터를 받고 작업을 수행

<br>

## AJAX의 장점

-   웹 페이지 전체를 다시 로딩하지 않고도, 웹 페이지의 일부분만을 갱신할 수 있습니다.
-   웹 페이지가 로드된 후에 서버로 데이터 요청을 보낼 수 있습니다.
-   웹 페이지가 로드된 후에 서버로부터 데이터를 받을 수 있습니다.
-   백그라운드 영역에서 서버로 데이터를 보낼 수 있습니다.

<br>

## AJAX의 한계

-   AJAX는 클라이언트가 서버에 데이터를 요청하는 클라이언트 풀링 방식을 사용하므로, 서버 푸시 방식의 실시간 서비스는 만들 수 없습니다.
-   AJAX로는 바이너리 데이터를 보내거나 받을 수 없습니다.
-   AJAX 스크립트가 포함된 서버가 아닌 다른 서버로 AJAX 요청을 보낼 수는 없습니다.
-   클라이언트의 PC로 AJAX 요청을 보낼 수는 없습니다.

### 클라이언트 풀링과 서버 푸시

클라이언트 풀링(client pooling) 방식이란 사용자가 직접 원하는 정보를 서버에게 요청하여 얻는 방식을 의미합니다. 이에 반해 서버 푸시(server push) 방식이란 사용자가 요청하지 않아도 서버가 알아서 자동으로 특정 정보를 제공하는 것을 의미합니다. 스마트 폰에서 각종 앱이 보내는 푸시 알림이 서버 푸시 방식의 대표적인 예입니다.

<br>

## AJAX 동작 원리

<p align="center">
    <img src="https://user-images.githubusercontent.com/22341362/144363650-c1829007-a24b-49d0-bdb0-16362416546d.png" alt="AJAX를 이용한 웹 응용 프로그램의 동작 원리" width="370" />
    <img src="https://user-images.githubusercontent.com/22341362/144363653-c0dcf329-9041-4285-a8c6-1b255fa3abe1.png" alt="기존 웹 응용 프로그램의 동작 원리" width=370 />
</p>

<br>

## HTTP request 만드는 방법

JavaScript를 이용하여 서버로 보내는 HTTP request를 만들기 위해서는 그에 맞는 기능을 제공하는 Object의 인스턴스가 필요합니다. `XMLHttpRequest`가 그러한 Object의 한 예입니다.

이러한 로직은 Internet Explorer의 `XMLHTTP`라고 불리는 ActiveX 객체로부터 시작되었습니다. 이후, Mozilla, Safari 등 기타 브라우저들이 Microsoft사의 ActiveX 객체의 메소드와 프로퍼티를 지원하는 `XMLHttpRequest` 객체를 적용합니다. 그러는 동안, Microsoft도 `XMLHttpRequest`를 적용합니다.

```javascript
// 구버전을 위한 호환성 코드입니다. 더 이상 이렇게 작성하지 않아도 됩니다.
var httpRequest;
if (window.XMLHttpRequest) {
    // Mozilla, Safari, IE7+, ...
    httpRequest = new XMLHttpRequest();
} else if (window.ActiveXObject) {
    // IE 6 이하
    httpRequest = new ActiveXObject("Microsoft.XMLHTTP");
}
```

서버에 요청을 하기에 앞서, 서버로 보낸 요청에 대한 응답을 받았을 때 어떤 동작을 할 것인지 정해야합니다. 위에서 생성한 `httpRequest`의 `onreadystatechange` property에 특정 함수(`nameOfTheFunction`)을 할당하면 요청에 대한 상태가 변화할 때 특정 함수가 불리게 됩니다.

```javascript
httpRequest.onreadystatechange = nameOfTheFunction;
```

주목할 사항으로는 위에서는 해당 함수를 수행하는 것이 아니라 단순하게 어떤 함수가 불릴 것인지만 지정한다는 점입니다. 단순하게 그 함수를 지정하는 것이므로 그 함수로 어떠한 변수도 전달하지 않습니다. 또한 단순하게 함수를 연결하면 되기 때문에 아래와 같이 "익명 함수"를 기입해도 됩니다.

```javascript
httpRequest.onreadystatechange = function () {
    // ...
};
```

위와 같이 서버로부터 응답을 받은 후의 동작을 결정한 뒤에 요청을 합니다. 아래와 같이 HTTP request 객체의 `open()`과 `send()`를 사용하면 요청을 할 수 있습니다.

```javascript
httpRequest.open("GET", "http://www.example.org/some.file", true);
httpRequest.send(null);
```

`open()` 메소드의 파라미터

-   첫 번째 파라미터 - HTTP request method
    -   GET, POST, HEAD 중의 하나이거나 당신의 서버에서 지원하는 다른 메소드입니다. 이 파라미터는 HTTP 표준에 따라 모두 대문자로 표기해야 합니다. 그러지 않으면 Firefox 같은 특정 브라우저는 이 request를 처리하지 않을 수도 있습니다.
-   두 번째 파라미터 - URL
    -   보안상의 이유로 서드 파티 도메인 상의 URL은 기본적으로 호출할 수 없습니다. 요구하는 모든 페이지의 정확한 도메인을 사용해야 합니다. 그렇지 않으면 "Permission Denied" 에러가 발생할 수 있습니다.
    -   `domain.tld` --> `www.domain.tld`
-   (생략 가능) 세 번째 파라미터 - Request가 비동기식으로 수행될 지를 결정
    -   `true` (기본값) - JavaScript 함수가 지속적으로 수행될 수 있어 서버로부터 응답을 받기 전에도 유저와 페이지의 상호작용이 계속 진행됩니다.
    -   `false` - 동기적으로 작동합니다. (`send()` 함수에서 서버로부터 응답이 올 때까지 기다립니다.)

`send()` 메소드의 파라미터는 POST 방식으로 요구한 경우 서버로 보내고 싶은 어떠한 데이터라도 가능합니다. 데이터는 서버에서 쉽게 parse할 수 있는 형식이어야 합니다.

```javascript
"name=value&anothername=" + encodeURIComponent(myVar) + "&so=on";
```

`multipart/form-data`, JSON, XML, SOAP 등과 같은 다른 형식도 가능합니다.

만약 POST 형식으로 데이터를 보내려 한다면, request에 MIME type을 먼저 설정해야 합니다. 예를 들어, `send()`를 호출하기 전에 아래와 같은 형태로 `send()`로 보낼 쿼리를 이용해야 합니다.

```javascript
httpRequest.setRequestHeader(
    "Content-Type",
    "application/x-www-form-urlencoded"
);
```

<br>

## 서버 응답에 대한 처리

서버로 요청을 보내기 전에, 위에서 서버의 응답을 처리하기 위한 JavaScript 함수의 이름을 지정했습니다.

```javascript
httpRequest.onreadystatechange = nameOfTheFunction;
```

이 함수는 무슨 일을 수행해야 할까요? 먼저, 해당 함수에서는 요구의 상태값을 검사할 필요가 있습니다. 만약 상태값이 `XMLHttpRequest.DONE`이라면, 서버로부터 모든 응답을 받았으며 이를 처리할 준비가 되었다는 것을 뜻합니다.

```javascript
if (httpRequest.readyState === XMLHttpRequest.DONE) {
    // 이상 없음, 응답 받았음
} else {
    // 아직 준비되지 않음
}
```

`readyState`가 가질 수 있는 모든 값의 목록은 `XMLHTTPRequest.readyState`에 작성되어 있으며, 아래와 같습니다.

-   0 (uninitialized) - request가 초기화되지 않음
-   1 (loading) - 서버와의 연결이 성사됨
-   2 (loaded) - 서버가 request를 받음
-   3 (interactive) - request를 처리하는 중
-   4 (complete) - request에 대한 처리가 끝났으며 응답할 준비가 완료됨

그 다음에는 HTTP 응답 상태 코드를 검사해야 합니다. 아래 예제에서는 AJAX 요청이 정상적으로 처리되었는지 아닌지만을 검사하기 위해 응답 코드가 "200 OK"인지 검사합니다.

```javascript
if (httpRequest.status === 200) {
    // 이상 없음!
} else {
    // 요구를 처리하는 과정에서 문제가 발생되었음
    // 예를 들어 응답 상태 코드는 404 (Not Founc) 이거나
    // 500 (Internal Server Error) 이 될 수 있음
}
```

이제 요구와 그에 대한 응답에 대한 상태 코드를 검사했으므로, 서버에서 받은 데이터를 통해 원하는 작업을 수행할 수 있습니다. 그리고 응답 데이터에 접근하기 위한 옵션이 2가지 있습니다.

1. `http_request.responseText` - 서버의 응답을 텍스트 문자열로 반환할 것입니다.
2. `http_request.responseXML` - 서버의 응답을 `XMLDocument` 객체로 반환하며, JavaScript의 DOM 함수들을 통해 이 객체를 다룰 수 있습니다.

위의 단계는 비동기식 요구를 사용했을 경우에 대한 설명입니다 (즉, `open()`의 세 번째 파라미터가 생략되었거나 `true`일 경우). 동기식 방법을 사용한다면 함수(`nameOfTheFunction`)를 명시할 필요 없이 `send()` 호출에 의해 반환되는 데이터를 바로 사용할 수 있습니다. 그러나 이는 스크립트가 `send()`를 호출할 때 멈춰지며 서버의 응답이 완료될 때까지 기다리기 때문에 나쁜 UX를 제공하게 합니다.

<br>

## 예제

우리가 작성한 JavaScript는 "I'm a test."라는 문장이 적힌 `test.html`이라는 HTML 문서를 요청해서 문서의 내용을 파라미터로 `alert()` 함수를 호출할 것입니다. 이 예제는 Vanilla JavaScript로 작성되었습니다. 또한 HTML, XML, PHP 파일들은 같은 경로에 위치되어 있어야 합니다.

```html
<button id="ajaxButton" type="button">Make a request</button>

<script>
    (function () {
        var httpRequest;
        document
            .getElementById("ajaxButton")
            .addEventListener("click", makeRequest);

        function makeRequest() {
            httpRequest = new XMLHttpRequest();

            if (!httpRequest) {
                alert("XMLHTTP 인스턴스를 만들 수가 없어요 ㅠㅠ");
                return false;
            }
            httpRequest.onreadystatechange = alertContents;
            httpRequest.open("GET", "test.html");
            httpRequest.send();
        }

        function alertContents() {
            if (httpRequest.readyState === XMLHttpRequest.DONE) {
                if (httpRequest.status === 200) {
                    alert(httpRequest.responseText);
                } else {
                    alert("request에 뭔가 문제가 있어요.");
                }
            }
        }
    })();
</script>
```

통신 에러 (서버가 다운되는 상황 등) 상황에서 status 필드에 접근하려 하면 `onreadystatechange` 메소드에서 예외 에러를 발생시킬 것입니다. 이러한 문제를 예방하기 위해서 `if..then` 구문을 `try..catch` 구문으로 감싸주세요.

```javascript
function alertContents() {
    try {
        if (httpRequest.readyState === XMLHttpRequest.DONE) {
            if (httpRequest.status === 200) {
                alert(httpRequest.responseText);
            } else {
                alert("request에 뭔가 문제가 있어요.");
            }
        }
    } catch (e) {
        alert("Caught Exception: " + e.description);
    }
}
```

<br>

## Reference

https://developer.mozilla.org/ko/docs/Web/Guide/AJAX/Getting_Started

http://tcpschool.com/ajax/ajax_intro_basic

http://tcpschool.com/ajax/ajax_intro_works
