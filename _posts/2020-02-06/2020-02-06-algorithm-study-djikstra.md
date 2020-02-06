---
category: "algorithm"
tags: 
  - "algorithm"
  - "blog"
---

## 백준 알고리즘 스터디 - 다익스트라 활용


### 백준 1504 - 특정한 최단 경로

https://www.acmicpc.net/problem/10473

시작점으로부터 다른 점들까지 거리 계산과 다른점들끼리의 거리 계산이 다르다.
조금 오래걸렸지만 바로 맞춤. 

<details><summary markdown="span">cpp solution</summary>

원의 임의의 한 점과 다른 원과의 거리 -> abs(diff-r)

정점 수가 많지 않으므로 인접 행렬로 선언.

```cpp

using namespace std;

int N,E,i,j;
ld a,b,c;

struct point{
    ld x;
    ld y;
};

point points[105];
// vector<pair<ld, int> > times[105];
ld times[105][105];
int s,e;
ld dist[105];
ld ans=BIG;
priority_queue<pair<ld,int>> pq;

void solve(){
    ld cx,cy,tx,ty;
    cin>>cx>>cy>>tx>>ty;
    cin>>N;
    points[0]=point{cx,cy};
    points[N+1]=point{tx,ty};
    rep1(N, i){
        cin>>a>>b;
        points[i]=point{a,b};
    }
    // cout<<points[N+1].x << ' '<<points[N+1].y<<endl;

    ld speed=5.0f;
    rep1(N+2, i){
        ld ix,iy,jx,jy;
        ix=points[i].x;
        iy=points[i].y;
        jx=cx;
        jy=cy;
        ld distance=sqrtl((ix-jx)*(ix-jx)+(iy-jy)*(iy-jy));
        times[0][i] = distance/speed;
        times[i][0] = 1.0f*MOD2;
    }

    rep1(N+2,i){
        dist[i]=1.0f*MOD2;
        rep1(N+2,j){
            if(i==j)continue;
            ld ix,iy,jx,jy;
            ix=points[i].x;
            iy=points[i].y;
            jx=points[j].x;
            jy=points[j].y;

            ld distance=sqrtl((ix-jx)*(ix-jx)+(iy-jy)*(iy-jy));
            // if(i==1&&j==2)
            // cout<<ix<< ' '<< iy<< ' '<< jx<< ' '<< jy<<' '<<distance<<endl;
            times[j][i]=times[i][j]=2.0f+(abs(distance-50)/speed);
        }
    }

    int start=0, end=N+1;
    dist[start]=0;
    pq.push({-0, -start});

    while(!pq.empty()){
        auto cur=pq.top();pq.pop();
        ld distan=-cur.first;
        int no=-cur.second;
        // cout<<distan<< '|'<<no <<endl;////

        rep1(N+1, i){
            if(dist[i]>dist[no]+times[no][i]){
                // cout<<dist[i]<< ' '<<dist[no]<< ' '<< times[no][i]<<endl;
                dist[i]=dist[no]+times[no][i];
                pq.push({-dist[i], -i});
            }
        }
    }

    cout<<dist[end]<<endl;
}


```

</details>



### 백준 1162 - 도로 포장

어렵다. 1번 틀림. 배열 넉넉한 범위를 for문 넉넉한 범위보다 좁게 잡아서 3런타임 미스.



포장을 한 경우를 어떻게 생각할까?
모든 경우를 다 고려한다면 10000C20이 되어 시간초과가 된다.

먼저 최단경로를 찾은 다음 포장해서는 안 된다. 
다른 경로가 포장을 하면 더 짧아질 수도 있기 때문이다.
일단 그럼 그런 예제를 만들어 보자.

4 4 1
1 2 10
2 4 10
1 3 1
3 4 100

주어진 예제 자체도 그런 예제에 속한다.
다익스트라 돌리면 1-2, 2-4로 가서 20이 나오는데,
포장을 하나 할 수 있다면 1-3, 3-4에서 3-4를 포장하면 비용은 1이 된다.

K가 작다는 것이 좀 중요해 보이는데, 아 생각해보니 도로의 수도 5만개밖에 안 된다. 정점 1만개를 잇는 모든 도로를 찾는다면 5만개보다 훨씬 많을텐데, 도로 개수가 5만개라면 꽤 적은 편에 속할 것이다.

다익스트라로 모든 도로를 돌려도 5만번->20번정도는 돌릴 수 있다?
근데 20번 돌릴 수 있다고 해도 잘 모르겠다.


다익->포장->다익->포장 이런 식으로 해야 하나?
다익->포장 했더니 포장했던 경로를 또 찾는다면?
위 예제를 보면, 1-2, 2-4에서 포장해봐야 1-2,2-4를 다시 찾을 뿐이다.
그러면 어떻게 해야 3-4를 고려하게 할까. 무조건 간선 중 큰 비용 순으로?
위 예제에서 3-4외 더 쓸모없는 경로가 1000이라 치면 3-4를 못 찾게 될 것이다.

그러면 모든 간선 포장 시도?
그러면 5만C20이 되어 또다시 시간 초과다.

하지만 여러 경로 중 1개를 포장할 수 있다고 하면, 최소 경로 길이가 1~2인 모든 경로를 다 찾아보아야 하지 않나?

모든 경로에 대해 몇 개 제외 했을 때의 최단 경로를 구하려면..

그렇다고 경로 길이로 제한해서도 안 되는 것이, 경로 길이 2짜리만 찾았는데, 1,1,1,1,1000 이런 경로가 포장시 최적일 수도 있다. 

도대체 어떻게 해야 하는 것인가. 대체 어떤 조건으로 저런 경로를 찾아야 하는 걸까?

<details><summary markdown="span">힌트 1</summary>
재귀 구조를 찾는다?
공간의 정의.
</details>

<details><summary markdown="span">힌트 2</summary>
(도시, 포장수)를 쌍으로 하는 정점을 다익스트라 탐색대상으로 생각한다.
</details>

<details><summary markdown="span">힌트 3</summary>
d(i,j)=i번째 도시까지 가는 j번의 포장을 한 경우의 최단거리

d(i,j)를 알 때, 도시 i와 연결된 도시 k에 대해,
 1. d(k,j+1) = min(d(k,j+1), d(i,j))   : i와 k사이의 도로를 포장하여 거리 단축.
 2. d(k, j) = min(d(k, j), d(i, j) + k~i 거리) : 현재 도시에서 포장수 냅두고 탐색

</details>

<details><summary markdown="span">cpp solution</summary>

결국 [힌트](https://www.acmicpc.net/board/view/2002)를 봤다. 
이런 식으로 해야 하는구나. 
"재귀 구조"를 찾아 활용하는 것으로 보이는데, 구현도 쉽지는 않을 것 같다. 

찾아야 하는 재귀 구조는 다음과 같다:
d(i,j)=i번째 도시까지 가는 j번의 포장을 한 경우의 최단거리

그런데 이걸 어떻게 재귀구조로 연결시킬지는 또 다른 문제다.
당연히 재귀구조를 활용해야 계산이 효율적으로 될 테니.

p(i,j)를 d(i,j)를 찾는 경로라고 하자.

d(i,j+1)=d(i,j)-max(p(i,j))일 것이다.
d(i+1,j)와 d(i,j)를 연결하는 방법은 잘 모르겠다. 그냥 모든 도시 i에 대해
d(i,0)부터 시작해서 하면 될 지도. 문제는 효율적으로 다음 max(p(i,j))를 찾아야 한다는 것이다.
그러면 모든 d(i,j)들에 대해 경로들을 담는 리스트 p(i,j)가 필요하다. 
그런데, 리스트의 최대를 찾고, 표시를 하던가, 최대를 제거해서 다음으로 넘겨야 하므로
참조로 넘겨도 될까 싶다.

다른 글을 보니 최소 힙에 대한 질문이 있던데 그걸 써야 하는 걸까?
아니면 그냥 우선순위 큐 구현때문인가.

잠깐 아니잖아. `d(i,j+1)=d(i,j)-max(p(i,j))일 것이다.` 이렇게 해버리면,
위 예제에서 d(N, 0)=20인데, d(N, 1)=1인 게 설명이 안 된다.

어, 그런데 d(i,j)로부터 본다면.. 

4 4 1
1 2 10
2 4 10
1 3 1
3 4 100

d(4,0)=d(2,0)+p(2,4)=10+10=20
d(3,0)=1
d(4,0)=d(3,0)+p(3,4)=1+100=101
d(4,1)=d(3,0)=1

즉 d(4,1)을 계산 할 때, 양방향임을 이용하여 가능한 이전 도시 중 최대로 비용을 깎는 도시를 찾아야 할 것이다. 그렇지만 바로 이전 도시가 아닐 수도 있어서.. 그런 경우는 어떻게 해야 하지?

0 1 2 3 4  5 6 7

0 5 5 5 5  5 5 5
0 1 1 1 99 1 1 1

이런 경우, d(7,1)은 위 경로가 아닌 아래쪽 경로를 쓰게 되어야 하는데,
아, 재귀적으로 연결시킨다면 이전 경로로부터 더하는 식으로만 한다면, 즉
d(6,1)=1*5가 나온다면 충분히 가능할 것이다. 문제는, 
d(4,1)의 계산 부분이다. d(3,1)=1+1+1 - 1=2이고, d(3,0)=3, d(4,0)=99+3.
여기에서, 99에 해당하는 간선의 그걸 빼야 한다는 걸 어떻게 효율적으로 찾을까?

d(i,j)는 연결된 도시 k들에 대해 d(k,j)+c(k->i) 또는,
d(i,j-1)에서 하나를 추가로 포장한 경우일 것..

d(4,1)에 대해 생각해 보자. 어느 경우에 해당하는가? 후자에 해당한다.
그런데 다시 생각해보자. 전자의 경우에서 이미 이전 도시에서 포장을 한 경우에 대해 처리가 되어있다. 그렇다면 후자의 새로 포장하는 경우에서 전자의 도시의 경로들에 대해 생각할 필요가 없는 것이다!!
즉 d(i,j-1)에서 새로 포장하는 경우에 대해 고려할 때에는, 바로 연결된 도시만 생각해도 된다는 것이다!

다시 정리한다면:
d(i,j-1)= 연결된 도시 k에 대해,
 1. d(k,j)+ k~i 거리
 2. d(k, j-1)
위 두가지의 최소.

그런데, 종료가 안 된다 왜지? 연결된 모든 도시가 아닌 이전 도시들에 
대해서만 봐야 할 거 같기도 한데.

그러면 visited를 쓰면 될 거 같다. 그런데, 어떻게 써야 하지? 꼬인다.
두 if 문에서 방문을 하긴 해야 하는데, 방문이 끝나면 방문을 취소해 두어야 한다. 그래야 다른 d에서 돌릴수 있기 때문.

이부분에서 계속 꼬인다. 결국 우선순위 큐쪽으로 다시 통합시켜서 풀었다. 노드의 정보가 두 가지여도 다익스트라를 돌리는 데에 같을때 정렬시키는 조건을 단다면? 문제가 되지 않는 모양이다.

```cpp

using namespace std;

int N,M,K;

struct road{
    ll length;
    int goal;
};

struct link{
    ll length;
    int goal;
    int usedPack;

    bool operator<(const link& rhs) const
    {
        if(length==rhs.length) return usedPack < rhs.usedPack;
        return length < rhs.length;
    }
};

vector<road> roads[10015];
priority_queue<link, vector<link> > pq;
ll dist[10015][23];
ll ans;
bool visited[10015][23];
int parent[10015];

// https://www.acmicpc.net/problem/1162

void solve(){
    cin>>N>>M>>K;
    int u,v,w;
    
    rep(M, i){
        cin>>u>>v>>w;
        roads[u].push_back(road{w,v});
        roads[v].push_back(road{w,u});
    }

    rep1(N+10, i){
        rep(K+2,j){
            dist[i][j]=MOD2;
        }
    }

    dist[1][0]=0;
    pq.push(link{-0, -1, 0});
    while(!pq.empty()){
        auto t=pq.top();pq.pop();
        int cur=-t.goal;
        int usedPack=t.usedPack;
        visited[cur][usedPack]=true;
        // if(usedPack>=K && cur!=N) continue;

        for(auto target: roads[cur]){
            ll length=target.length;
            int goal=target.goal;

            if(!visited[goal][usedPack] && dist[goal][usedPack]>dist[cur][usedPack]+length){ // updatable
                parent[goal]=cur;
                dist[goal][usedPack]=dist[cur][usedPack]+length;
                pq.push(link{-dist[goal][usedPack], -goal, usedPack});
            }
            if(!visited[goal][usedPack+1] && usedPack<K && dist[goal][usedPack+1]>dist[cur][usedPack]){
                dist[goal][usedPack+1]=dist[cur][usedPack];
                pq.push(link{-dist[cur][usedPack], -goal, usedPack+1});
            }
        }
    }

    cout<<dist[N][K]<<endl;
}

```

그런데, 우선순위 큐를 썼기 때문인지 visited를 쓰지 않아도 통과가 된다.
visited 뺀것.

```cpp

using namespace std;

int N,M,K;

struct road{
    ll length;
    int goal;
};

struct link{
    ll length;
    int goal;
    int usedPack;

    bool operator<(const link& rhs) const
    {
        if(length==rhs.length) return usedPack < rhs.usedPack;
        return length < rhs.length;
    }
};

vector<road> roads[10015];
priority_queue<link, vector<link> > pq;
ll dist[10015][23];
ll ans;
int parent[10015];

// https://www.acmicpc.net/problem/1162

void solve(){
    cin>>N>>M>>K;
    int u,v,w;
    
    rep(M, i){
        cin>>u>>v>>w;
        roads[u].push_back(road{w,v});
        roads[v].push_back(road{w,u});
    }

    rep1(N+10, i){
        rep(K+2,j){
            dist[i][j]=MOD2;
        }
    }

    dist[1][0]=0;
    pq.push(link{-0, -1, 0});
    while(!pq.empty()){
        auto t=pq.top();pq.pop();
        int cur=-t.goal;
        int usedPack=t.usedPack;
        // if(usedPack>=K && cur!=N) continue;

        for(auto target: roads[cur]){
            ll length=target.length;
            int goal=target.goal;

            if(dist[goal][usedPack]>dist[cur][usedPack]+length){ // updatable
                parent[goal]=cur;
                dist[goal][usedPack]=dist[cur][usedPack]+length;
                pq.push(link{-dist[goal][usedPack], -goal, usedPack});
            }
            if(usedPack<K && dist[goal][usedPack+1]>dist[cur][usedPack]){
                dist[goal][usedPack+1]=dist[cur][usedPack];
                pq.push(link{-dist[cur][usedPack], -goal, usedPack+1});
            }
        }
    }

    cout<<dist[N][K]<<endl;
}

```

왜 재귀로 했을 때 실패했는지 잘 모르겠다. bfs를 써야 하는건가?

</details>