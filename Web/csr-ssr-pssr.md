# CSR / SSR / PSSR

## CSR (Client Side Rendering)

웹서버는 HTML, CSS, JS 형태의 파일을 클라이언트에게 전달해주는 전달자 역할만 합니다. 클라이언트는 웹서버로부터 다운받은 파일을 실행하고, JS 파일을 실행하면서 DOM에 내용을 추가하면서 화면을 그리게 됩니다.

### 단점

-   초기에 JS를 읽어서 무엇을 어떻게 그려야 할지를 판단하고 그리는 작업을 하는 동안 딜레이가 존재합니다.
-   index.html에는 단순히 뼈대만 존재하기 때문에 검색 엔진 봇들이 크롤링을 할 때 아무 내용이 없어 SEO에 취약합니다.

### 장점

-   첫 화면을 구성한 이후 작업에 있어서는 서버의 의존 없이 클라이언트 혼자 그릴 수 있으므로 빠른 화면 전환이나 인터랙션이 가능합니다.

<br>

## SSR (Server Side Rendering)

SSR은 HTML, CSS, JS를 직접 올리는 것이 아니라, 해당 파일들을 만들어 낼 수 있는 로직을 올립니다. 서버는 해당 로직을 실행해서 HTML, CSS, JS를 만들어 클라이언트에게 전달합니다. 만들어진 HTML 파일에는 이미 DOM이 다 구성되어 있습니다.

로직을 올린다는 것은 결국 실행 가능한 Function을 올린다는 것이고, 이는 백엔드 프레임워크에 해당됩니다.

### 장점

-   클라이언트는 이미 DOM이 다 구성된 파일을 받기 때문에 초기 구동 속도가 빠릅니다.
-   DOM에 내용이 들어있기 때문에 검색 엔진들이 정보를 수집할 수 있어 SEO에 좋습니다.

<br>

## Thinking

### CSR이 초기 구동이 느리고 SSR이 빠르다?

JS 기반 프레임워크는 CSR이든 SSR이든 주최가 다를 뿐, 누군가는 JS를 읽어서 DOM을 구성해야 합니다. 만약에 서버가 오래된 팬티엄이고 내 PC가 i9 최신 모델이면, 서버에서 JS를 읽어서 구성하는 것보다 내 PC가 JS를 읽어서 구성하는 것이 훨씬 더 빠릅니다.

게다가 서버는 다수를 상대하기 때문에 수백~수천의 유저에게 DOM을 구성하여 보내주어야 하지만, 내 PC는 하나만 구성하면 됩니다.

서버에서는 캐싱을 적용할 수 있지 않냐고 반문할 수 있고, 실제로도 SSR은 주로 캐싱과 함께 구현하긴 하지만, 이는 정적인 컨텐츠가 많은 경우에만 유효한 것이고 동적인 컨텐츠가 많으면 결국 캐싱의 활용도가 떨어지고 서버에 부담은 그대로 갑니다.

### CSR은 SEO에 취약하다?

CSR이 SEO에 취약한 것은 사실이나, 그 전에 앞서 내 서비스가 SEO가 필요한가를 고민해봐야 합니다. 모든 서비스가 SEO가 필요한 것은 아닙니다. 어떤 서비스는 고객의 데이터를 보호하기 위해 SEO가 일어나지 않게끔 해야 합니다. 공개되어야 하는 퍼블릭 데이터만 SEO가 필요할 뿐이지, 모든 데이터가 SEO가 필요하지는 않습니다.

### 비용 문제

CSR로 구성할 경우 S3같은 단순 스토리지에 올리고 전면에 Cloud Front로 구성하여 캐싱과 레이턴시를 줄이고 트래픽 비용만 지불하게 됩니다. SSR의 경우 매번 서버에서 로직을 실행하는 비용을 추가하게 됩니다. 위에서 언급한 바와 같이 동적 컨텐츠가 많은 경우에는 캐싱도 무효화됩니다. 인스턴스를 Auto Scale하거나 Serverless 기반에 Function을 사용하더라도 CSR과 비교해서 비용이 수백~수천배 차이가 날 수 있습니다.

<br>

## PSSR (Progressive Server Side Rendering)

PSSR은 서버에서 중요한 컨텐츠를 먼저 렌더링하여 클라이언트에게 전송하고 중요하지 않은 컨텐츠는 이후 렌더링 되는대로 전송합니다. 브라우저는 중요한 컨텐츠를 먼저 보여주고 중요하지 않은 컨텐츠는 이후 차례대로 보여줍니다.

### PSSR의 동작

1. 클라이언트가 서버에게 HTML을 요청합니다.
2. 서버는 먼저 중요한 컨텐츠를 렌더링하여 클라이언트에게 전송합니다.
3. 클라이언트는 중요한 컨텐츠를 받아서 화면에 보여줍니다.
4. 서버는 중요한 컨텐츠를 렌더링한 후, 중요하지 않은 컨텐츠를 렌더링하고 클라이언트에게 전송합니다.
5. 클라이언트는 중요하지 않은 컨텐츠를 받아서 화면에 보여줍니다.
6. 페이지가 모두 로드되면, DOM 요소에 이벤트 핸들러 등을 설정합니다.

### PSSR의 장점

-   서버에서 중요한 컨텐츠가 렌더링 되는 즉시 중요하지 않은 컨텐츠가 렌더링 될 때까지 기다리지 않고 클라이언트에게 전송합니다.
-   컨텐츠가 JavaScript bundle load에 제약을 받지 않습니다.
-   CSR과 SSR에 비해 빠릅니다.

### PSSR의 단점

-   이벤트 리스너는 화면이 모두 렌더링 된 후에 동작합니다. 화면이 빨리 보여도, 중요하지 않은 컨텐츠까지 렌더링 되어야 이벤트가 가능합니다.
-   Progressive rendering을 위한 framework가 없고 웹 애플리케이션과 그 한계에 제약을 받습니다.

<br>

## Reference

https://tech.weperson.com/wedev/frontend/csr-ssr-spa-mpa-pwa/#csr-client-side-rendering-vs-ssr-server-side-rendering

https://medium.com/the-thinkmill/progressive-rendering-the-key-to-faster-web-ebfbbece41a4