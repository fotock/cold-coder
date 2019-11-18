# GitHub

GitHub 好地方。好地方有好风光。

## 一些统计数据

https://gitstar-ranking.com

## GraphQL

第4版 API 使用了 GraphQL，取代了之前的 REST API, 功能及灵活性上更强。

1. 查询示例中。 `after` 某个 cursor 可用于分页。
2. 截至 18年3月15日，暂时不支持查询 Repository 的 Contributers, 除非你已参与该 Repository。

- Repository 搜索

1. 库总数

```graphql
{
  search(type: REPOSITORY, query: "stars:<1000000", first: 1) {
    repositoryCount
  }
}
```

2. 查询最多关注的库

```graphql
{
  search(type: REPOSITORY, query: "stars:>8000", first: 10, after: "Y3Vyc29yOjQ=") {
    repositoryCount
    edges {
      cursor
      node {
        ... on Repository {
          id
          name
          description
          url
          diskUsage
          createdAt
          updatedAt
          owner {
            login
          }
          primaryLanguage {
            name
          }
          watchers {
            totalCount
          }
          stargazers {
            totalCount
          }
          forks {
            totalCount
          }
          issues {
            totalCount
          }
          pullRequests {
            totalCount
          }
          commitComments {
            totalCount
          }
          languages(first: 3) {
            edges {
              node {
                name
              }
            }
            totalCount
          }
        }
      }
    }
  }
}
```

3. 查询用户/机构的库

```graphql
{
  search(type: REPOSITORY, query: "user:google sort:stars-desc", first: 100) {
    userCount
    edges {
      cursor
      node {
        ... on Repository {
          id
          stargazers {
            totalCount
          }
        }
      }
    }
  }
}
```

- User 搜索

1. 用户总数

```graphql
{
  search(type: USER, query: "followers:<1000000", first: 40) {
    userCount
  }
}
```

2. 最受关注用户

```graphql
{
  search(type: USER, query: "followers:>5000", first: 100) {
    userCount
    edges {
      node {
        ... on User {
          login
          name
          url
          avatarUrl
          location
          company
          followers {
            totalCount
          }
        }
      }
    }
  }
}
```

- User 详情搜索

```graphql
{
  user(login: "torvalds") {
    name
    url
    avatarUrl
    location
    company
    followers {
      totalCount
    }
    repositories {
      totalCount
    }
    issues {
      totalCount
    }
    issueComments {
      totalCount
    }
    contributedRepositories {
      totalCount
    }
    pullRequests {
      totalCount
    }
  }
}
```

## Excellent Repositories

### [streisand](https://github.com/StreisandEffect/streisand)

#### Valid connections

- openconnect-cli (Mac)
- ss (Mac)
- AnyConnect (Android)
- WireGuard (Android)
