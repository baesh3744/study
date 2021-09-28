# RESTful API

## REST란

-   REST란 REpresentational State Transfer의 약자입니다.
-   REST는 Resource Oriented Architecture입니다. API 설계의 중심에 자원(resource)이 있고, HTTP Method를 통해 자원을 처리하도록 설계하는 것입니다.

<br>

## REST 6가지 원칙

1. Uniform interface
2. Stateless
3. Caching
4. Client-Server
5. Hierarchical system
6. Code on demand

<br>

## "RESTful하게 API를 디자인 한다"의 의미

1. 리소스와 행위를 명시적이고 직관적으로 분리합니다.

    - 리소스는 URI로 표현되는데 리소스가 가리키는 것은 명사로 표현되어야 합니다.
    - 행위는 HTTP Method로 표현하고, GET(조회), POST(생성), PUT(기존 entity 전체 수정), PATCH(기존 entity 일부 수정), DELETE(삭제)를 분명한 목적으로 사용합니다.

2. Message는 Header와 Body를 명확하게 분리해서 사용합니다.

    - Entity에 대한 내용은 body에 담습니다.
    - 애플리케이션 서버가 행동할 판단의 근거가 되는 컨트롤 정보인 API 버전 정보, 응답받고자 하는 MIME 타입 등은 header에 담습니다.
    - Header와 body는 HTTP Header와 HTTP Body로 나눌 수도 있고, HTTP Body에 들어가는 json 구조로 분리할 수도 있습니다.

3. API 버전을 관리합니다.

    - 환경은 항상 변하기 때문에 API의 signature가 변경될 수 있음에 유의해야 합니다.
    - 특정 API를 변경할 때는 반드시 하위호환성을 보장해야 합니다.

4. 서버와 클라이언트가 같은 방식을 사용해서 요청하도록 합니다.
    - 서버와 클라이언트가 json 형식으로 보내든, form-data 형식으로 보내든 하나로 통일해야 합니다.
    - URI가 플랫폼 중립적이어야 합니다.

<br>

## RESTful API의 장점

1. Open API를 제공하기 쉽습니다.
2. 멀티플랫폼 지원 및 연동이 용이합니다.
3. 원하는 타입으로 데이터를 주고 받을 수 있습니다.
4. 기존 웹 인프라(HTTP)를 그대로 사용할 수 있습니다.

<br>

## RESTful API의 단점

1. 사용할 수 있는 메소드가 4가지밖에 없습니다.
2. 분산환경에는 부적합합니다.
3. HTTP 통신 모델에 대해서만 지원합니다.

<br>

## Reference

https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Development_common_sense#restful-api
