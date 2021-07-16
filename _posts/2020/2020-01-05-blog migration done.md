---
template: "post"
category: "blog"
tags: 
  - "blog"
banner: "/assets/bg/2.jpg"
toc: true
---

## Blog migration done

Migrated all the past posts manually.

But search does not work. Is it because of Korean letter?



결국 minimal-mistakes를 쓰기로 했다. 디자인도 좋고 빠른 검색기능 및 사이즈 전환시 애니메이션까지 되고 속도도 좋다. 특징이라면 포스트 & slug 형식이 어느정도 지정되어있다.
기본적으로 경로가 `<category>/<slug>` 형식으로 지정되는데, slug는 따로 지정하지 않으면 `title` 또는 `<title from filepath>`가 된다. filepath는 `YYYY-MM-DD-title`형식을 지켜야 한다. slug를 지정하면 똑같이 `<category>/<custom slug>`형식으로 url이 정해진다.

toc를 추가할 수 있는데, dark skin이랑 잘 안 어울리는 부분이 있다.

사이트 설정에서 skin을 설정하는 식이라 수동으로 직접 추가하지 않는 한 동적 skin을 쓰기는 어려울 듯 싶다. 기본검색은 50단어까지만 한다고 한다. full-text-search를 쓸 수 있지만 포
포스트 로딩이 느려질 수 있다고 한다.