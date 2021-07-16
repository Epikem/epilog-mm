---
category: "algorithm"
tags: 
  - "algorithm"
  - "blog"
  - "programmers"
  - "python"
---

## 프로그래머스 파이썬 문제풀이

### 완전탐색 1. 모의고사

내 풀이

<details><summary markdown="span">py solution</summary>

```py
import collections

def solution(answers):
    answer = []
    hits=[0,0,0]
    p1=[1,2,3,4,5,1,2,3,4,5]
    p2=[2,1,2,3,2,4,2,5]
    p3=[3,3,1,1,2,2,4,4,5,5]
    c1=collections.Counter()

    for i in range(len(answers)):
        cura=answers[i]
        if(cura==p1[i%len(p1)]):
            c1[0]+=1
        if(cura==p2[i%len(p2)]):
            c1[1]+=1
        if(cura==p3[i%len(p3)]):
            c1[2]+=1
    maxv=max(max(c1[0],c1[1]), c1[2])
    for i in range(3):
        if(maxv==c1[i]):
            answer.append(i+1)
    return answer
```

</details>

다른사람 풀이 (모든사람 풀이에 공개되있으니 괜찮겠지)

<details><summary markdown="span">py solution</summary>

다른 점:
1. 패턴 배열들을 따로 담지 않고 이중 p배열에 선언한 후 `enumerate`로 돌렸다.
2. `enumerate`를 적극적으로 활용 (iterable에 대해 idx, value 순으로 값을 가져오므로 귀찮게 `for i in range(len(list))`같이 쓸 필요가 없다!)
3. 배열의 최대값을 `max(list)`로 바로 얻었다. 이게 되는구나!
4. 최대값 갖는 사람 결과 배열을 list comprehension을 사용하여 생성

```py
def solution(answers):
    p = [[1, 2, 3, 4, 5],
         [2, 1, 2, 3, 2, 4, 2, 5],
         [3, 3, 1, 1, 2, 2, 4, 4, 5, 5]]
    s = [0] * len(p)

    for q, a in enumerate(answers):
        for i, v in enumerate(p):
            if a == v[q % len(v)]:
                s[i] += 1
    return [i + 1 for i, v in enumerate(s) if v == max(s)]
```

</details>


### 완전탐색 2. 소수 찾기

내 풀이

<details><summary markdown="span">py solution</summary>

처음에 에라토스테네스의 체를 비효율적인 방법으로 구현하여 틀림. (1~1천만까지 돌리면서 기존 소수로 나눠보기). 1천만 정도의 배열은 너무 크다고 생각했기 때문에 이렇게 했는데, 다른 구현을 보니 그냥 1천만짜리 set를 만들어서 지워나가는 식으로 되어있었다.

중복되지 않는 임의 길이 비복원 조합?? 을 만들기가 꽤나 까다로웠다.

```py

import itertools
from itertools import chain,combinations,permutations
sets=set()

lis=[]

def powerset(iterable):
    global sets
    "powerset([1,2,3]) --> () (1,) (2,) (3,) (1,2) (1,3) (2,3) (1,2,3)"
    s = list(iterable)
    for v in list(chain.from_iterable(permutations(s, r) for r in range(len(s)+1))):
        if(len(v)==0): continue
        val=int(''.join(map(str,list(v))))
        sets.add(val)

def solution(numbers):
    global lis
    answer = 0
    
    n=10000000
    prime=set([i for i in range(3, n+1, 2)])
    prime.add(2)
    for i in range(3, n+1, 2):
        if i in prime:
            prime-=set([i for i in range(i*2, n+1, i)])
    prime=list(prime)
    prime.sort()
    count=0
        
    arr=list(map(int,list(numbers)))
    lis=arr
    
    ps=powerset(lis)
    for i,v in enumerate(sets):
        left=0
        right=len(prime)-1
        while(left<right):
            mid=int((left+right)/2)
            if(prime[mid]==v):
                count+=1
                break
            if(prime[mid]<v):
                left=mid+1
            else:
                right=mid
        
    return count

```
</details>

다른 풀이

<details><summary markdown="span">py solution</summary>

이해하기도 어렵다. 어떻게 이렇게 짜는 걸까??

0. set를 사용하여 간결하게 표현. (`가능한 조합` - `모든 소수가 아닌 수`)가 답이 된다.
1. 모든 뽑기 수 조합을 길이 0~i~n-1 permutation set에 집어넣되, 중간에 문자열로 만들었다가 다시 숫자로 만들어서 빈 순열은 제외된다. 
2. 소수 계산할 때 제곱근까지만 계산한다.
3. 소수를 제외하는 계산을, 추가하는 방식이 아니라, set를 빼는방식으로 계산한다.
  모든 i에 대해, i는 빼지 않고, i*2부터 모든 수를 set에서 빼는 식으로 계산하다 보면 결국 set에는 소수만 남게 된다.

- `max(a)`로 `set`의 최대값을 얻을 수 있다
- `set`를 `range`로 초기화시킬 수 있다.

```py

from itertools import permutations
def solution(n):
    a = set()
    for i in range(len(n)):
        a |= set(map(int, map("".join, permutations(list(n), i + 1))))
    a -= set(range(0, 2))
    for i in range(2, int(max(a) ** 0.5) + 1):
        a -= set(range(i * 2, max(a) + 1, i))
    return len(a)
```

</details>

