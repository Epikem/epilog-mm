---
category: "algorithm"
tags: 
  - "algorithm"
  - "blog"
---

## 백준 알고리즘 스터디 - 그래프 4주차 MST (Minimum Spanning Tree)

### 문제 목록:

1922 - 네트워크 연결
6497 - 전력난
1647 - 도시 분할 계획
4386 - 별자리 만들기
2406 - 안정적인 네트워크
2887 - 행성 터널
1944 - 복제 로봇
2211 - 네트워크 복구
9373 - 복도 뚫기
2255 - 트리 만들기
1626 - 두 번째로 작은 스패닝 트리


### 백준 1922 - 네트워크 연결

https://www.acmicpc.net/problem/1922

어떻게 푸는지 모르겠어서 [블로그](https://blog.naver.com/kks227/220799105543) 보고 함.

<details><summary markdown="span">cpp solution</summary>

글 보고 대충 구현해봤는데 2번 틀림. 유니온 파인드를 재귀식으로 구현해야 한다.

```cpp

groups[a]=groups[groups[a]];

```

이런 식으로 구현해서는 틀린다.

```cpp

// https://www.acmicpc.net/problem/1922

int TC,i,j,k,a,b,c,s,e,t;

int ans;

vector<pair<int, ii> > roads;

int groups[1005];
int cnt=0;

int find(int a){
    if(a==groups[a]) return a;
    groups[a]=find(groups[a]);
    return groups[a];
}

void merge(int a, int b){
    a=find(a);
    b=find(b);
    if(a>b){
        int tmp=a;
        a=b;
        b=tmp;
    }
    groups[b]=groups[a];
}

void solve(){
    int n,m;
    cin>>n>>m;
    rep1(n, i){
        groups[i]=i;
    }
    rep1(m, i){
        cin>>a>>b>>c;
        roads.push_back({c, {a, b}});
    }

    sort(all(roads)+1);

    rep1(m, i){
        // cout<<roads[i].first << ' '<<'\n';
        int x=roads[i].second.first,y=roads[i].second.second;
        if(find(x)==find(y)) continue;
        else{
            ans+=roads[i].first;
            cnt++;
            merge(x,y);
            if(cnt==n-1) break;
        }
    }

    cout<<ans<<endl;
}

```

</details>


### 백준 6497 - 전력난

https://www.acmicpc.net/problem/6497

<details><summary markdown="span">cpp solution</summary>

원리 자체는 간단. 전체 비용에서 MST 돌려서 선택된 비용을 빼면 된다.

로컬에서 20만개 엣지를 돌리려 하니 터지길래 205개로 줄여서 돌리다가 그대로 제출해서 1 런타임 에러

고쳐봐도 에러나서 정점번호 0부터 시작하는데 나는 대체로 1부터를 기준으로 풀고 있었으므로 `a++; b++`을 추가하니 맞음. 그런데, 왜 이게 꼭 필요한지, 어디서 틀리게 되는건지는 잘 모르겠다.

```cpp

using namespace std;

// https://www.acmicpc.net/problem/6497

ll TC,i,j,k,a,b,c,s,e,t;

int ans;

int gs[200005];
int cnt=0;

int find(int node){
    if(node==gs[node]) return node;
    return gs[node]=find(gs[node]);
}

bool merge(int n1, int n2){
    n1=find(n1);
    n2=find(n2);
    if(n1==n2) return false;
    gs[n2]=n1;
    return true;
}

struct edge{
    ll u,v,w;
    edge(): edge(-1,-1,0){}
    edge(ll u1, ll v1, ll w1): u(u1),v(v1),w(w1){}
    bool operator <(const edge& e) const {return w<e.w;}
};

void solve(){
    edge edges[200005];
    while(true){
        int n,m;
        cin>>m>>n;
        if(m==0&&n==0) {
            break;
        }

        ll total=0;
        ll sub=0;
        int cnt=0;

        rep1(m, i){
            gs[i]=i;
        }

        rep1(n, i){
            cin>>a>>b;
            a++;b++;
            cin>>c;
            total+=c;
            edges[i]={a,b,c};
        }

        sort(edges, edges+n);

        rep1(n, i){
            a=edges[i].u, b=edges[i].v, c=edges[i].w;
            if(find(a)==find(b))continue;
            else {
                merge(a,b);
                cnt++;
                sub+=c;
                if(cnt==m-1) break;
            }
        }

        cout<<total-sub<<'\n';
    }
}

```

</details>



### 백준 4386 - 별자리 만들기



<details><summary markdown="span">cpp solution</summary>

이차원 배열을 만들어 푸려고 했으나, 정렬하는데 문제가 있어서 일차원 구조체로 바꿈. 대신에 인덱싱을 이차원으로 했다.

```cpp

// https://www.acmicpc.net/problem/4386

float dists[105][105];
pair<float, float> positions[105];

struct link{
    int u,v;
    float w;
    link(): link(-1.0f,-1.0f,1000005.0f){};
    link(int u1, int v1, float w1): u(u1), v(v1), w(w1){};

    bool operator <(const link& e) const { return w<e.w; } 
};

int uf[105];

int find(int a){
    if(a==uf[a]) return a;
    return uf[a]=find(uf[a]);
}

bool merge(int a, int b){
    a=find(a);
    b=find(b);
    if(a==b) return false;
    uf[a]=b;
    // cout<<"merging "<<a<<' '<<b<<endl;
    return true;
}

void solve(){
    link links[10300];
    int n;
    cin>>n;
    int i; int j;
    float x,y;
    rep1(n, i){
        cin>>x>>y;
        positions[i]={x,y};
        uf[i]=i;
    }

    rep1(n, i){
        rep1(n, j){
            float x1=positions[i].first,x2=positions[j].first;
            float y1=positions[i].second,y2=positions[j].second;
            dists[i][j]=(x1-x2)*(x1-x2)+(y1-y2)*(y1-y2);
            links[i*n+j]={i, j, dists[i][j]};
        }
    }

    sort(links, links+10300);

    int cnt=0;
    int a=0,b=0;
    float cost=0.0f;
    rep1(10300, i){
        a=links[i].u,b=links[i].v;
        if(!merge(a,b)) continue;
        cost+=sqrtf(links[i].w);
        cnt++;
        // cout<<links[i].w<<endl;
        if(cnt==n-1)break;
    }
    
    cout<<setprecision(8)<<cost<<endl;
}

```


</details>


### 백준 2406 - 안정적인 네트워크

#### 삽질

고장이 최대 한 번만 난다는 것일거라 가정하고, mst로 연결하면 특정 컴퓨터가 고장나면 끊어져버릴텐데. 그렇다고 완전연결을 할 필요는 없어 보인다.

mst는 정렬할 때 `O(e lg e)`시간이 걸리고, 실제 노드 추가는 `O(e)`정도 걸리는거 같은데, 그러면 모든 노드에 대해 제거하고 mst 돌려보는걸 반복해도 괜찮지 않을까.

주어진 예제에서 왜 5-4만 연결하면 된다는 거지? 1번이 고장나면 2번도 고립될 텐데.

아. 3-2링크가 있어서, 1-3,3-2 링크로 연결된다.
어 그러면. 본사인 1번과는 모두 연결되어 있으니, 1번과의 존재를 빼고 나서 mst 돌리면 되려나?

그러면 될거 같긴 한데 mst 돌리려면 {링크,비용} 형태로 값이 저장되어야 정렬을 할 텐데 인접 행렬로 주어져 있는데다 몇 개의 간선은 이미 연결되어있다. 이부분을 어떻게 처리할 지가 애매하다.

그런데, 이미 연결되어있다는것은.. 연결 비용을 0으로 바꾸는 걸로 생각해도 되지 않을까? -> (좋지 않음)

<details><summary markdown="span">hint1</summary>
문제에 따라 본사와 다른 지사들은 이미 연결되어 있다.
</details>

<details><summary markdown="span">cpp solution</summary>
기본적으로 본사를 제외하고 mst 돌리면 된다.

그외 어려운 점

1. 이전 문제처럼 주어진 형태가 인접 행렬 형태라서 정렬을 위해 형태를 바꾸어야 한다.
2. 이미 주어진 지사간 연결의 처리. 처음엔 비용을 0으로 하면 되지 않을까 했는데, 문제에서 구하는 것이 합쳐야 하는 링크들이기 때문에, 그러면 추가적으로 처음에 연결했는지에 대해서도 기록해야 하는 등 복잡해짐. 주어진 연결된 지사들은 그냥 유니온 파인드 merge를 해두면 된다.

원리는 대충은 알겠는데 계속 틀려서 다른 답 참조함. 본사 1을 제외하는걸 제대로 못했음.

```cpp

    rep1(n, i){
        rep1(n, j){

            cin>>tmp;
            cost[i][j]=min(cost[i][j], tmp);
            
            // // if(i==1 || j==1) cost[i][j]=30005;
            if(i>=j){
                edges.push_back({i, j, cost[i][j]});
            }
        }
    }

```

위와 같이 되어있으면 본사에 대한 엣지도 추가되므로 틀리게 된다.

```cpp

// 안정적인 네트워크
// https://www.acmicpc.net/problem/2406 

const int MAXN=1005;

struct edge{
    int u,v,w;
    edge(): edge(-1,-1,0){};
    edge(int u1, int v1, int w1): u(u1), v(v1), w(w1) {};
    bool operator <(const edge& e) const {
        return w<e.w;
    }
};

bool conn[MAXN][MAXN];
int cost[MAXN][MAXN];
int uf[MAXN];
int cnt;
int total=0;

vector<edge> edges;

int find(int a){
    if(a==uf[a]) return a;
    return uf[a]=find(uf[a]);
}

bool merge(int a, int b){
    a=find(a);
    b=find(b);
    if(a==b) return false;
    uf[a]=b;
    return true;
}

void solve(){
    int n,m,i,j,k,a,b,c,t,tmp;
    cin>>n>>m;

    if(n==1){
        cout<<"0 0"<<endl;
        return;
    }

    rep1(n, i){
        uf[i]=i;
        rep1(n, j){
            cost[i][j]=3000500;
        }
    }

    uf[1]=2;

    rep(m, i){
        cin>>a>>b;
        merge(a,b);
    }

    rep1(n, i){
        rep1(n, j){

            cin>>tmp;
            cost[i][j]=min(cost[i][j], tmp);
            
            // // if(i==1 || j==1) cost[i][j]=30005;
            if(i>=j && i!=1 && j!=1){
                edges.push_back({i, j, cost[i][j]});
            }
        }
    }

    auto it=edges.begin();
    sort(it, it+edges.size());

    cnt=0;
    total=0;

    vii links;

    
    rep(edges.size(), i){
        a=edges[i].u,b=edges[i].v,c=edges[i].w;
        if(!merge(a,b)) continue;
        
        cnt++;
        total+=c;
        links.push_back({a,b});
        // cout<<edges[i].u<<' '<<edges[i].v<< ' '<<edges[i].w<<endl;
        if(links.size()==n-1) break;
    }

    cout<<total<<' ' <<links.size()<<endl;
    rep(links.size(), i){
        a=links[i].first,b=links[i].second;
        cout<<links[i].first << ' '<<links[i].second<<'\n';
    }
}

```

</details>
