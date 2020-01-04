---
date: "2019-04-21"
title: "algorithm"
category: "algorithm"
tags: ["algorithm"]
banner: "/assets/bg/4.jpg"
---
알고리즘 공부

--------------------------

1. [algospot](#algospot)
   1. [와일드카드 - WILDCARD](#와일드카드---wildcard)
2. [tags](#tags)

## algospot

### 와일드카드 - WILDCARD

https://algospot.com/judge/problem/read/WILDCARD

처음에는 시그니쳐를 `match(w,t,ia,ib)` 이렇게 했다가 성가셔서 w,t 문자열 매개변수를 밖으로 뺐다. `*`의 매치 조건을 어떻게 해야 하는지 좀 헷갈려서 한 번 오답이 났다. 두가지 경우라고 생각했는데 0문자 이상이므로 다음과 같이 3가지 or 조건이다. 0문자 매치하고 나가는 경우를 생각 안 해버렸다. 그리고 중간에 메모이제이션을 했더니 다 true가 되버리는 문제가 있었는데, 캐시 초기화를 안해서였다.
매 번 캐시 접근할 때 인덱싱으로 하기 귀찮은데, cpp의 참조자 같은 건 없을까?
함수로 만들면 좀더 편할지도?


- 1문자 매치하고 나가기 (ia+1,ib+1)
- 1문자 매치하고 ia는 홀딩 (ia, ib+1)
- 0문자 매치하고 나가기 (ia+1, ib)

## tags
  \#algorithm, \#algospot, \#python