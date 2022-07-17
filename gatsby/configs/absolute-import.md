# 절대 경로 사용하기

Gatsby에서 절대 경로를 사용하려면 다음과 같이 설정한다.

- AS-IS: `import Header from '../../components/header'`
- TO-BE: `import Header from 'src/components/header'`

```ts
// gatsby-node.ts
import path from "path";
import type { GatsbyNode } from "gatsby";

export const onCreateWebpackConfig: GatsbyNode["onCreateWebpackConfig"] = ({
  actions,
}) => {
  actions.setWebpackConfig({
    resolve: {
      modules: [path.resolve(__dirname), "node_modules"],
    },
  });
};
```

```json
// tsconfig.json
// For eslint
{
  "compilerOptions": {
    "baseUrl": "./"
  }
}
```

## Reference

- https://www.gatsbyjs.com/docs/how-to/custom-configuration/add-custom-webpack-config/
