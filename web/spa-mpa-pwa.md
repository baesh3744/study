# SPA / MPA / PWA

## SPA (Single Page Application) vs. MPA (Multi Page Application)

SPA는 하나의 페이지(Single Page)로 구성된 웹이고, MPA는 여러 개의 페이지(Multi Page)로 구성된 웹입니다.

기술적으로 SPA는 어떤 페이지를 접속하더라도 동일한 HTML, CSS, JS 파일을 이용하여 접속한 페이지에 맞게 화면을 구성하고, MPA는 페이지별로 HTML, CSS, JS 파일을 받아서 화면을 구성합니다. SPA는 페이지를 이동하면, JS를 통해 현재 보고 있는 DOM의 내용을 모두 제거하고, 다른 컨텐츠로 DOM을 채운 다음에 브라우저상에 URL을 강제로 변경하여, 실제로 방문한 적 없는 URL을 방문하여 해당 페이지를 보고 있는 느낌을 주는 것입니다.

MPA는 고전적인 방식이지만 아직도 많이 사용되고 있습니다. 최근 JS 프레임워크들은 SPA를 기본으로 하고 있습니다.

<br>

## PWA (Progressive Web App)

PWA는 웹과 네이티브 앱의 기능을 모두 갖춘 형태입니다.

간단하게는 웹처럼 URL로 접근하여 애플 스토어나 구글 스토어를 거치지 않고 모바일 디바이스 홈 화면에 바로 설치하여 사용이 가능하다는 것입니다. 설치하여 구동 시 상단에 URL 컨트롤을 감추어 일반적인 모바일 앱처럼 작동하게 만들 수도 있습니다. 굳이 설치를 하지 않더라도 모든 데이터를 기본적으로 캐싱하여 오프라인 상태에서도 데이터를 볼 수 있습니다.

<br>

## Reference

https://tech.weperson.com/wedev/frontend/csr-ssr-spa-mpa-pwa/#spa-single-page-application-vs-mpa-multi-page-application
