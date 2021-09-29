# HTTP Method

## GET

-   GET은 리소스로부터 데이터를 요청할 때 사용합니다.
-   URL을 통해 쿼리를 요청합니다.
    -   리소스 주소에 `?`를 붙이고 `name=value` 형식으로 쿼리를 전송합니다.
    -   `/test/demo_form.php?name1=value1&name2=value2`
-   GET 요청은 캐싱이 가능합니다.
-   GET 요청은 브라우저 기록에 남습니다.
-   GET 요청은 북마크될 수 있습니다.
-   쿼리가 URL을 통해 요청되므로, 길이에 제한이 있고, 보안이 필요한 민감한 데이터를 다루기에는 적합하지 않습니다.
-   데이터를 수정할 수 없습니다.

<br>

## POST

-   POST는 서버에 데이터를 보내어 리소스를 생성/수정할 때 사용합니다.
-   HTTP Request Message의 body를 통해 데이터를 전송합니다.
-   POST 요청은 캐싱, 북마크가 불가능하고, 브라우저 기록에 남지 않습니다.
-   데이터 길이에 제한이 없습니다.

<br>

## PUT

-   PUT은 POST와 마찬가지로 리소스의 생성/수정할 때 사용합니다.

### POST vs PUT

-   POST
    -   클라이언트가 특정 URL로 생성 POST를 요청하면, 서버 측에서는 해당 URL의 아래에 리소스를 생성합니다.
    -   `/items`으로 POST 요청을 보낼 경우, `/items/1`에 리소스가 생성됩니다.
    -   따라서, `/items`으로 같은 요청을 여러 번 보내면, 요청 횟수만큼 `/items` 아래에 리소스가 생성됩니다.
-   PUT
    -   PUT 요청은 클라이언트가 지정한 URL에 리소스를 생성합니다.
    -   따라서, 같은 요청을 여러 번 보내더라도, 하나의 리소스만 존재합니다.

<br>

## HEAD

-   HEAD는 GET과 동일하지만, 서버에서 body를 리턴하지 않습니다.
-   GET Method를 사용하기 전에 HEAD Method의 Response의 상태 코드를 통해, 리소스의 수정, 존재 여부를 확인할 수 있습니다.

<br>

## DELETE

-   DELETE는 요청 리소스를 삭제합니다.
-   HTTP 규격에는 클라이언트의 요청에도 서버가 요청을 무력화시킬 수 있도록 정의되어 있습니다. 따라서 DELETE Method는 항상 보장되지는 않습니다.

<br>

## PATCH

-   PATCH는 요청 리소스의 일부를 수정할 때 사용합니다.

<br>

## OPTIONS

-   OPTIONS는 서버가 지원 가능한 HTTP Method를 알아보기 위해 사용합니다.

<br>

## Reference

https://www.w3schools.com/tags/ref_httpmethods.asp
https://medium.com/@lyhlg0201/http-method-d561b77df7
