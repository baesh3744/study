# 쿼크 모드와 표준 모드

## 쿼드 모드와 표준 모드

과거 웹 페이지는 Netscape Navigator 용과 Microsoft Internet Explorer 용의 두 가지 버전으로 만들어졌습니다. W3C에서 웹 표준을 제정할 당시, 기존 브라우저들은 새롭게 만들어진 표준을 기반으로 대부분의 웹 사이트들을 제대로 표현할 수 없었습니다. 따라서, 브라우저들은 새로운 표준으로 제작된 사이트와 예전 방식으로 제작된 사이트를 렌더링하기 위한 두 가지 모드를 제공했습니다.

웹 브라우저의 레이아웃 엔진에는 쿼크 모드(quirks mode), 거의 표준 모드(almost standards mode), 표준 모드(standards mode)의 세 가지 방식이 존재합니다. 쿼크 모드(quirks mode)에서는 기존 방식으로 제작된 웹 사이트들을 표현하기 위해 Navigator 4와 인터넷 익스플로러 5의 비표준 동작을 에뮬레이션합니다. 완전 표준 모드(full standards mode; 이하 표준 모드)에서는 HTML과 CSS에 의해 웹 페이지가 표시됩니다. 거의 표준 모드(almost standards mode)는 소수의 쿼크 모드 요소만 지원합니다.

<br>

## 브라우저는 무엇을 보고 모드를 결정할까?

웹 브라우저는 HTML 페이지를 쿼크 모드나 표준 모드 중 무엇으로 렌더링할지 결정할 때 문서의 첫 부분에 기술된 DOCTYPE을 참조합니다.

웹 페이지를 표준 모드로 렌더링하려고 한다면 아래와 같이 페이지 첫 부분에 DOCTYPE을 기술해야 합니다.

```html
<!DOCTYPE html>
<html>
    ...
</html>
```

위에서 기술된 <!DOCTYPE html>은 HTML5에서 권장하는 가장 간단한 방식입니다.

DOCTYPE은 반드시 HTML 문서 첫 부분에 기술해야 합니다. 인터넷 익스플로러 9 또는 그 이전 버전에서는 DOCTYPE 이전에 주석이나 XML 선언부 등 무엇이든 작성된 상태라면 해당 문서를 쿼크 모드로 렌더링하게 됩니다.

HTML5에서 DOCTYPE의 유일한 목적은 표준 모드를 활성화하기 위함입니다. 이전 버전의 HTML 표준에서는 DOCTYPE이 추가적인 의미를 갖지만, 실제를 이를 쿼크 모드와 표준 모드의 판단 이외의 목적으로 사용한 브라우저는 없습니다.

<br>

## XHTML

웹 페이지가 Content-Type HTTP 헤더를 application/xhtml+xml MIME 타입으로 설정함으로써 XHTML로 제공된다면, 해당 페이지는 항상 표준 모드로 렌더링되기 때문에 DOCTYPE을 기술할 필요가 없습니다.

XHTML-like 컨텐츠를 text/html MIME 타입으로 제공한다면 웹 브라우저는 이를 HTML로 인식하므로, 표준 모드로 렌더링하려면 DOCTYPE을 기술해야 합니다.

<br>

## Reference

https://developer.mozilla.org/ko/docs/Web/HTML/Quirks_Mode_and_Standards_Mode
