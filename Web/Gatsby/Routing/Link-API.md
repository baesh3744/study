# Link API

## `<Link>`

`<Link>` 컴포넌트는 사이트 내에서 페이지를 이동할 때 사용합니다.

Gatsby는 prefetch를 통해 페이지에서 사용할 리소스의 로딩 속도를 높입니다. `<Link>` 컴포넌트가 포함된 페이지가 로드되면, Gatsby는 해당 페이지와 연결된 페이지를 미리 로드하기 시작합니다. 그래서 사용자가 링크를 클릭하는 순간, 다음 페이지를 바로 렌더링할 수 있습니다.

<br>

## `<Link>` 사용 방법

`<a>` 태그 대신 `<Link>` 컴포넌트를 사용합니다. 이 때, href 대신 to를 사용합니다.

```jsx
import React from "react";
import { Link } from "gatsby";

const Page = () => (
    <div>
        <p>
            Check out my <Link to="/blog">blog</Link>!
        </p>
        <p>
            {/* Note that external links still use `a` tags */}
            Follow me on <a href="https://twitter.com/gatsbyjs">Twitter</a>!
    </div>
);
```
