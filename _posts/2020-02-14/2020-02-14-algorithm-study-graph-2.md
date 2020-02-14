---
category: "algorithm"
tags: 
  - "algorithm"
  - "blog"
---

## 백준 알고리즘 스터디 - 그래프 2주차 벨만 포드와 플로이드 워셜

### 11403 - 경로 찾기

https://www.acmicpc.net/problem/11403

쉬운 문제


### 1389 - 케빈 베이컨의 6단계 법칙

문제 잘못 봐서 2미스, 실수로 2미스

<details><summary markdown="span">cpp solution</summary>

처음엔 가장 적은 케빈 베이컨 수를 출력하라는건줄 알고 틀렸고, 다음엔

친구관계 입력받을 때 M갯수로 받아야 하는데 또 N으로 받았다.

```cpp

int N, M;
vi fs[105];
int dist[105];
bool visited[105];

// 각 사람까지의 단계 수

void solve(){
    cin>>N>>M; int i,j,a,b,k;
    rep1(M,i){
        cin>>a>>b;
        fs[a].push_back(b);
        fs[b].push_back(a);
    }
    int ansV=999999;
    int ans=0;
    rep1(N,i){
        int ansT=0;
        memset(visited, false, 105*sizeof(bool));
        priority_queue<ii> pq;
        rep1(N, k){
            dist[k]=999999;
        }

        dist[i]=0;
        pq.push({0, -i});
        while(!pq.empty()){
            auto t=pq.top(); pq.pop();
            int dis=-t.first;
            int nn=-t.second;

            for(auto nav: fs[nn]){
                if(dis+1<dist[nav]){
                    dist[nav]=dis+1;
                    pq.push({-dist[nav], -nav});
                }
            }
        }

        rep1(N, k){
            ansT+=dist[k];
            // cout << dist[k] << ' ';
        }
        // cout<<endl;

        if(ansT<ansV){
            ansV=ansT;
            ans=i;
        }
    }
    cout<<ans<<endl;
    
}

```

</details>


### 11404 - 플로이드

내가 플로이드를 두번 돌려야 결과가 맞게 나오는 이유를 찾아냈다. 경유점의 for문이 바깥에 있어야 하는데 안쪽에 넣어두고 돌리고 있었던 것.

<details><summary markdown="span">cpp solution</summary>

```cpp

int city[105];
vii paths[105];
int dists[105][105];

void solve(){
    int n,m,i,j,k,a,b,c;
    cin>>n>>m;
    
    rep1(n, i){
        rep1(n, j){
            dists[i][j]=9999999;
        }
    }

    rep1(m,i){
        cin>>a>>b>>c;
        // paths[a].push_back({b,c});
        dists[a][b]=min(dists[a][b], c);
        dists[a][a]=0;
        dists[b][b]=0;
    }

    rep1(n,k){
        rep1(n,i){
            rep1(n,j){
                dists[i][j]=min(dists[i][j], dists[i][k]+dists[k][j]);
            }
        }
    }

    rep1(n, j){
        rep1(n, i){
            if(dists[j][i]==9999999) cout<<0<< ' ';
            else cout<<dists[j][i] << ' ';
        }
        cout<< '\n';
    }
}

```

</details>


### 2358 - 키 순서



학생수는 최대 500,
링크수는 250000.

어떻게 접근해야 하나?

자기보다 큰 자 보다 작은 자가 나와 링크가 없거나,
자기보다 작은 자 보다 큰 자가 링크가 없으면 안 된다.

하지만 이렇게 구현해버리면, 자기를 제외한 모든 자들의 모든 링크를 검사하므로 결국 500*250000정도가 된다. 그러면 시간초과인가?


<details><summary markdown="span">hint 1</summary>

아! 자기보다 큰 학생이 몇 명이고, 자기보다 작은 학생이 몇 명인지 알 수 있으면 될 것이다!

</details>


<details><summary markdown="span">cpp solution</summary>

예제의 경우는,

taller(1)=4 (5가 아니다! 연결된 학생들 중에서.)
taller(5)=3
taller(3)=3
taller(4)=2
taller(6)=0
taller(2)=0

smaller(1)=0
smaller(3)=0
smaller(5)=1
smaller(4)=smaller(3)+smaller(5)=smaller(3)+smaller(1)+1=1+2=3

taller(4)+smaller(4)=2+3=5로, 학생수-1이면 이 학생은 자기 키를 정확히 알 수 있다.

taller(1)+smaller(1)=4+0=4<5로 알 수 없다.

그러면, taller와 smaller를 따로 구해야 하는 걸까??
그리고, taller와 smaller자체를 애초에 어떻게 구하지??

아는 정보:
- 각자 자기 기준으로 자기보다 큰/작은 녀석의 정보

알아야 하는 정보:
- 각자 자기보다 큰/작은 녀석의 수

재귀로 구해 들어갈 테니, 자기보다 작은 녀석을 찾지 못하면 자기가 0을
반환하는 식으로 한다면?? 

smaller(tallerone)=smaller(smallerone)+1
taller(smallerone)=taller(tallerone)+1

이런 식이 될 것인데, 뭔가 부족한 느낌이 든다.
어디를 어떻게 시작점으로 잡아야 하지?

어느 시작점을 잡든 찾아나가게 하려면??
양쪽으로 검색을 해야 할 것이다.
아니다. 한쪽으로 하고, 모든 학생에 대해 돌려도 될 것이다.

문제는, 더해나가는 식으로 세면 중복해서 세는 경우가 생긴다는 것.

결국 매 학생마다 전부 초기화하며 모두 visit하며 세는 방식으로 구현했다. (그렇게 해도 시간초과 안 남, visited면 나가므로 모든 간선들을 검사하게 되지 않는 모양이다.)

92ms로 성공.

```cpp

using namespace std;

int students[505];
vi taller[505];
int tallers[505];
vi smaller[505];
int smallers[505];
bool visited[505];
bool visitedsmallers[505];
int target;

void getTallers(int n){
    if(visited[n]) return;
    visited[n]=true;
    tallers[target]++;
    
    for(auto tallerPeople:taller[n]){
        getTallers(tallerPeople);
    }
}

void getSmallers(int n){
    if(visitedsmallers[n]) return;
    visitedsmallers[n]=true;
    
    smallers[target]++;
    for(auto smallerPeople:smaller[n]){
        getSmallers(smallerPeople);
    }
}

void solve(){
    int n,m,i,j,k,a,b,c;
    cin>>n>>m;

    rep1(m, i){
        cin>>a>>b;
        
        taller[a].push_back(b);
        smaller[b].push_back(a);
    }

    int ans=0;
    rep1(n, i){
        memset(visited, false, 505*sizeof(bool));
        memset(visitedsmallers, false, 505*sizeof(bool));
        target=i;
        getTallers(i);
        
        getSmallers(i);
        // cout<<i<<' ' <<smallers[i]<< ' '<<tallers[i]<<endl;
        if(tallers[i]+smallers[i]==n+1){
            ans++;
        }
    }
    
    cout<<ans<<endl;
}

```

</details>


