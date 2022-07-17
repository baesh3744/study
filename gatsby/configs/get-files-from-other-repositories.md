# 다른 레포지토리에서 파일 가져오기

우선, 다음 플러그인 2개를 설치한다.

```console
npm install --save gatsby-source-git
npm install gatsby-source-filesystem
```

다음과 같이 설정한다.

> `gatsby-source-filesystem`을 설정하지 않으면 에러가 발생한다.  
> [Github issue](https://github.com/stevetweeddale/gatsby-source-git/issues/22)

```ts
// gatsby-config.ts
import type { GatsbyConfig } from "gatsby";

const config: GatsbyConfig = {
  // ...
  plugins: [
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `contents`,
        path: `${__dirname}/contents`,
      },
    },
    {
      resolve: `gatsby-source-git`,
      options: {
        name: `til`,
        remote: `https://github.com/baesh3744/today-i-learned.git`,
        branch: `master`,
        patterns: ["**/*"],
      },
    },
    // ...
  ],
};

export default config;
```

설정 후, 다음과 같이 파일을 가져올 수 있다.

```ts
// gatsby-node.ts
import path from "path";
import type { GatsbyNode } from "gatsby";

interface QueryResult {
  errors?: any;
  data?: {
    allFile: {
      edges: {
        node: {
          name: string;
          relativeDirectory: string;
        };
      }[];
    };
  };
}

// Implement the Gatsby API "createPages". This is called once the
// data layer is bootstrapped to let plugins create pages from data.
export const createPages: GatsbyNode["createPages"] = async ({
  graphql,
  actions,
  reporter,
}) => {
  const { createPage } = actions;

  // Query for markdown nodes to use in creating pages.
  const result: QueryResult = await graphql(`
    query {
      allFile(filter: { name: { ne: "README" }, extension: { eq: "md" } }) {
        edges {
          node {
            name
            relativeDirectory
          }
        }
      }
    }
  `);

  // Handle errors
  if (result.errors) {
    reporter.panicOnBuild(`Error while running GraphQL query.`);
    return;
  }

  // Create pages for each markdown file.
  const template = path.resolve("src/templates/Template.tsx");
  result.data?.allFile.edges.forEach(({ node }) => {
    const pagePath = `${node.relativeDirectory}/${node.name}`;
    createPage({
      path: pagePath,
      component: template,
    });
  });
};
```

## Reference

- https://hmajid2301.medium.com/how-to-manage-your-gatsby-blog-posts-from-another-repo-with-gitlab-ci-80091bd20055
- https://www.gatsbyjs.com/plugins/gatsby-source-git/
- https://www.gatsbyjs.com/docs/creating-and-modifying-pages/#creating-pages-in-gatsby-nodejs
