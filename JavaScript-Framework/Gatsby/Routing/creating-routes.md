# Creating Routes

## 1. `src/pages`

Gatsby는 `src/pages` 디렉토리에 존재하는 `.js` 파일의 이름으로 페이지를 생성합니다. 이 때, 파일의 경로가 브라우저의 경로가 됩니다.

```bash
└─ src
    └─ pages
        ├─ index.js
        ├─ contact.js
        └─ information
            └─ info.js
```

`src/pages/contact.js` 파일은 `yoursite.com/contact` 페이지를, `src/pages/information/info.js` 파일은 `yoursite.com/information/info` 페이지를 생성합니다.

예외적으로 `index.js` 파일은 파일이 속한 디렉토리로 매칭됩니다. 즉, `src/pages` 디렉토리 안에 존재하는 `index.js` 파일은 `yoursite.com` 페이지를 생성합니다. `src/pages/information` 디렉토리에는 `info.js` 파일만 존재하므로, `yoursite.com/information/info` 페이지에는 접속할 수 있지만, `yoursite.com/information` 페이지에는 접속할 수 없습니다.

<br>

## 2. File system route API

(나중에)

<br>

## 3. `gatsby-node.js`

(나중에)

<br>

## Reference

https://www.gatsbyjs.com/docs/reference/routing/creating-routes/
