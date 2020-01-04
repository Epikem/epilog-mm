---
title: "blog template consideration"
slug: "posts/2020-01-04-blog-template-consideration"
date: "2020-01-04"
template: "post"
category: "meta"
tags: 
  - "meta"
  - "blog"
banner: "/assets/bg/3.jpg"
---

## 블로그 관리

gatsby-lumen으로 할 지, jekyll minimal-mistakes로 할 지 결정했었는지 기억이 안 나는데..

어느쪽이든 체계적인 포스트 관리 툴이 없는거같아 조금 문제다. netlify-cms를 써야 하는걸까??

그게 포스트 작성도 도와주나?



찾아본 결과 netlify-cms보다 contentful이 낫다는데, 5000컴포넌트 이상은 유료라고 한다. 일단은 그냥 쓰되, 사실 더 큰 문제는 포스트 작성보다도 이전 데이터의 관리인 것 같다. 같은 gatsby의 템플릿끼리도 이전 블로그 템플릿에서 쓰던 frontmatter정보와 현재 템플릿이 다른데, 그 때마다 migration 스크립트를 써주기도 어려운 노릇이다.

### 이전 블로그의 frontmatter 예시:

```yaml
---
date: "2019-04-14"
title: "블로그 관리 통합 스크립트 개발"
category: "meta"
tags: ["meta"]
banner: "/assets/bg/3.jpg"
---
```

### 현재 블로그의 frontmatter 예시:

```yaml
---
title: "blog-"
slug: "posts/blog-"
date: "2020-01-04"
template: "post"
category: "meta"
tags: 
  - "meta"
  - "blog"
banner: "/assets/bg/3.jpg"
---
```

template와 slug가 추가되었다. banner도 다른 이름이었는데 바꾸었다.

### gatsby-lumen vs jekyll minimal-mistakes

> gatsby-lumen

1. gatsby lumen의 경우 검색 기능을 지원하지 않는 것 같다.

물론 vscode에서 작업한다면 나에겐 상관 없겠지만, 정보 저장용으로 쓰는 부분도 크기 때문에 mmistakes의 검색 기능도 상당히 끌린다.

직접 검색기능을 구현하는것도 좋겠지만 시간이 꽤 걸릴 듯 하다.

2. gatsby를 로컬에서 돌려보려면 `node_modules`를 설치해야 하는데, dropbox를 쓰고 있어서 곤란해진다.


> jekyll minimal-mistakes

1. `frontmatter`의 `date`형식이 날짜가 아니라 정확한 표준 시각 형식이다

```yaml
date: 2010-01-07T15:34:30-04:00
last_modified_at: 2016-03-09T16:20:02-05:00
```

2. github-pages remote theme 기능을 사용할 경우, 로컬에서 돌릴 방법이 없는것 같다.
github-pages를 어차피 쓰긴 하겠지만, 포스트를 실시간으로 보면서 수정하고 싶어도 느려진다는 단점이 있다.





