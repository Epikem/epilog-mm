---
category: "algorithm"
tags: 
  - "algorithm"
  - "blog"
---

## 백준 알고리즘 스터디 - 그래프 3주차 벨만 포드와 플로이드 워셜 2

### 백준 11657 - 타임머신

https://www.acmicpc.net/problem/11657

벨만 포드 구현 문제.

<details><summary markdown="span">cpp solution</summary>

edge relaxation을 수행하는 현재 위치에 대한 거리가 INF 일 때에는 업데이트에서 제외해야 하는데, 그걸 놓쳐서 3미스.

```cpp

using namespace std;

// https://www.acmicpc.net/problem/11657

int TC,i,j,k,a,b,c,s,e,t;

pair<int, ii> roads[6006];
int dists[505];

void solve(){
    int n,m;
    cin>>n>>m;
    rep1(m, i){
        cin>>a>>b>>c;
        roads[i]={a,{b,c}};
    }
    rep1(n, i){
        dists[i]=BIG;
    }

    int ans=-1;
    bool updated=false;
    dists[1]=0;
    rep1(n+2, i){
        updated=false;
        rep1(m, j){
            auto road=roads[j];
            int start=road.first;
            int end=road.second.first;
            int len=road.second.second;
            if(dists[start]<BIG && dists[start]+len<dists[end]){
                dists[end]=dists[start]+len;
                updated=true;
            }
        }
    }
    if(updated){
        cout<<-1<<endl;
    } else {
        rep1(n-1, i){
            if(dists[i+1]>=BIG) cout<<-1<<'\n';
            else cout<<dists[i+1]<<'\n';
        }
    }

}

```

</details>


### 백준 11562 - 백양로 브레이크

조금 생각해야하는 문제. 방향 그래프를 주고, 주어진 경로를 탐색하기 위한 최소의 양방향화 횟수 구하기.

<details><summary markdown="span">thoughts</summary>

양방향으로 연결된 경우, 자유자재로 돌아다닐 수 있으므로 그룹으로 볼 수 있다.

그렇지만 양방향에 단방향이 붙어있는 경우는?
특정 노드로 갔다가 돌아서 자기 자신으로 돌아올 수 있다면 그 노드는
같은 그룹이다. 그렇지만 이렇게 구현하려면.. 모든 노드들에 대해 (250)
다른 노드로 갔다가 (*250) 다시 자기자신으로 (*다익스트라?) 돌아와야 하니
시간이 너무 오래 걸릴 듯 하다.

어떻게 해야 최소의 필요한 양방향 수를 알 수 있을까. 도로는 최대 약 9만개.

논리적으로 생각하자. 

아는 것: 특정 정점 a->b 도달 가능성. (0 또는, 1)
알아야 할 것: 특정 정점 a->b 도달 위한 양방향 도로 수

a->b, c->d 에서 즉 특정 경로에서 어느 경로를 양방향으로 만들지 결정해야 한다.

그런데, 주어진 경로만 사용하게 되므로, 정방향 일방통행으로 도달하지 못하는 점은 그 둘을 뒤집어서 도달 가능성을 찾으면 도달 가능하지 않을까?

a<->b->c 에서, c->a경로의 개설가능성을 찾기 위해, a->c를 찾아보면, a->b->c 경로를 찾게 된다. 그러면 그런 경로 중 반대방향 경로가 없는 경우가 최대한 적은 경로가 답이 된다. 그러면 c->a 확인을 위해 a->c로 가는 모든 경로를 찾아야 하나?

플로이드 워셜을 쓴다면.. O(V^3)이라 9만*300=2700만으로 너무 큰 감이 있는데.. 게다가, 반대방향 경로의 존재여부를 어떻게 체크해야 할 지도 모르겠다. 그렇다면, 아예 반대방향 경로의 존재 여부자체를 거리처럼 생각해서, 다익스트라처럼 탐색하되, 반대방향 경로가 있는 모든 경로들을 다 찾고, 반대방향 경로가 1개 없는 경우들을 찾고... 이런 식으로 구현이 가능할까?? 큐에 넣을 때, 반대방향... ...
근데, 애초에 다익스트라는 한 시작점에서 다른 모든 점을 찾을 때 쓰는 알고리즘이라..

그치만 위의 반대방향 개수 기준 bfs를 돌려서 타겟 정점을 찾는다면 그 시점의 반대방향 없는 개수가 답이 될 거 같다!

(풀 때는 bfs 안씀)

</details>

<details><summary markdown="span">hint1</summary>

양방향화를 통해 길을 만들어야 하므로, 주어지지 않은 새 길을 만들 일은 없다.

</details>

<details><summary markdown="span">hint2</summary>

길 u,v가 있다면, u에서 v로 갈 때 양방향 길을 만들 필요는 없다.

</details>

<details><summary markdown="span">hint 3</summary>
길 u,v가 있다면, 다른 길이 없을 때 v에서 u로 갈 때 양방향 길을 하나 만들어야 한다.
</details>

<details><summary markdown="span">cpp solution</summary>

길  u->v가 주어졌다면, u->v 비용을 0, v->u cost를 1로 설정하고, (양방향 길이면 둘 다 0) n이 작으므로 플로이드-워셜 돌려서 해결.

```cpp

// https://www.acmicpc.net/problem/11652

int TC,i,j,k,a,b,c,s,e,t;

int roads[255][255]; // a->b path
int counts[255][255]; // a->b 필요한 양방향 수

void solve(){
    int n,m;
    cin>>n>>m;
    int u,v,b;
    rep1(250,i){
        rep1(250, j){
            counts[i][j]=BIG;
        }
    }
    rep1(250,i){
        counts[i][i]=0;
    }
    rep1(m, i){
        
        cin>>u>>v>>b;
        roads[u][v]=1;
        counts[u][v]=0;
        counts[v][u]=1;
        
        if(b==1){
            roads[v][u]=1;
            counts[v][u]=0;
        }
    }
    
    rep1(n, k){
        rep1(n, i){
            rep1(n, j){
                if(k==i || k==j) continue;
                
                if(counts[i][j] > counts[i][k]+counts[k][j]){
                    counts[i][j]=counts[i][k]+counts[k][j];
                }
            }
        }
    }

    int k;cin>>k;
    rep1(k, i){
        int s,e;
        cin>>s>>e;

        cout<<counts[s][e]<<'\n';
    }

}

</details>