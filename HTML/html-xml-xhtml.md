# HTML/XML/XHTML

## HTML (HyperText Markup Language)

HTML은 원래 마크업 언어를 만들기 위한 일종의 SGML의 응용 프로그램입니다. SGML은 상당히 복잡해서 브라우저 대부분이 이를 따르지 않았고, 실제 웹에서 사용되는 HTML은 SGML의 영향을 받은 커스터마이징된 마크업 언어입니다.

### 한계

-   HTML User Agent가 오류에 대해서 관대합니다.
-   잘못된 태그 사용 또는 중첩되거나 닫히지 않은 태그 등을 작성하여도 브라우저는 문제가 있는 부분을 허용하거나 무시합니다.
-   데이터의 의미보다는 기술된 태그의 외향적인 표현에 집중되어 있습니다.
-   정보의 구조화 및 데이터 간의 연관성 표현과 유효성 검증이 어렵습니다.

<br>

## XML (eXtensible Markup Language)

-   기존 HTML의 한계를 보완하기 위해 만들어진 마크업 언어입니다.
-   웹에서 HTML의 고정된 어휘 이상의 것을 사용하고 싶은 욕구에서 시작되었습니다.
-   SGML과 같은 마크업 언어지만, 일반 parser를 더 쉽게 만들 수 있도록 단순화한 언어입니다.
-   XML의 X는 eXtensible 즉, 확장성의 의미를 담고 있으며, 이는 미리 정의된 태그가 아닌 커스텀 태그를 정의해 사용할 수 있다는 장점을 의미합니다.
-   HTML과 같은 문서의 형태를 보여주는 게 아닌, 데이터 전달 및 표현에 초점이 맞춰져 있습니다.
-   XML은 표현과 내용이 완전히 분리되어 데이터 구조 및 내용 기술이 가능하며, stylesheet를 사용하여 다방면의 데이터 표현이 가능합니다.

<br>

## XHTML (eXtensible HyperText Markup Language)

-   기존 HTML과 XML의 장점을 하나로 합친 개념입니다.
-   보다 구조화된 형식과 엄격한 문법을 갖고 있습니다.
-   XHTML은 향상된 웹사이트 검색기능과 복잡한 데이터 처리가 가능합니다.
-   엄격한 규칙을 사용하고 있어 문서가 손상될 가능성이 있습니다.

<br>

## HTML/XHTML 결정 기준

MIME Type (HTTP Content-Type 헤더) 을 통해 문서가 HTML인지 XHTML인지 결정할 수 있습니다.

-   text/html로 제공되는 경우 HTML로 처리됩니다.
-   Application/xhtml+xml 또는 text/xml로 제공되는 경우 XHTML로 처리합니다.

<br>

## Reference

https://www.js2uix.com/frontend/xml-xhtml-diff/
