---
category: "algorithm"
tags: 
  - "algorithm"
  - "blog"
---

## 백준 알고리즘 스터디 - 다익스트라 활용 2

### 백준 10282 - 해킹

https://www.acmicpc.net/problem/10282

단순 다익스트라 문제. 알고리즘 또 헷갈려서 2미스. (1,2: 자료형 문제인줄 알음.)

<details><summary markdown="span">cpp solution</summary>

다익스트라만 잘 적용하면 걸리는 시간은 유효 감염 노드중에서 최대 시간을 재면 된다.

아래는 내가 틀린 이유.

int의 자료형 저장 범위가 10억 얼마까지라고 본 기억이 있어서 의존성 개수 d~10만, 각 의존성 길이 s~1천이므로 곱하면 딱 1억이다. 따라서 최대값을 1억+1천 이상의 수로 잡으면 괜찮다고 생각함. 그리고 맞은 후 int로 다시 바꿔 제출해보니 그건 사실이었다. 문제는 다익스트라 알고리즘 적용부분에서 헷갈린 것. 이웃 노드를 검사할 때 `nav.first`가 노드번호, `nav.second`는 *현재 노드에서 이웃 노드까지의 거리*이다. 그런데 다익스트라를 적용할 때 비교할 것은 *시작점으로부터 현재 노드까지 알려진 거리 `dist[cur]`+현재 노드로부터 이웃노드까지의 거리`nav.second`*가 *시작점으로부터 알려진 이웃노드까지의 거리`dist[nav.first]`*보다 짧은지로 업데이트한다.

그런데, 큐에 넣는 거리는 다음에 꺼낼 시기를 알려주며, **시작 노드로부터의 거리**를 기준으로 늘려나가며 업데이트하는 식이므로, 큐에 넣어야 할 다음 탐색 노드 pair는 `{dist[nav.first], nav.first}`가 되어야 한다. 그런데 `{nav.second, nav.first}`로 잘못 넣어서 틀렸다.

```cpp

using namespace std;

int N,M,K;
vector<pair<ll, ll> > paths[11111];
priority_queue<pair<ll, ll> > pq;
ll dist[11111];

void solve(){
    int t,tt;cin>>t;
    rep1(t, tt){
        // cout<<"test start\n";
        ll n,d,c;cin>>n>>d>>c;
        ll i,a,b,s;

        rep1(n, i){
            dist[i]=100201020;
            paths[i].clear();
        }

        rep1(d, i){
            cin>>a>>b>>s;
            paths[b].push_back({a,s});
        }

        rep1(n, i){
            dist[i]=100201020;
        }

        dist[c]=0ll;
        pq.push({-0ll, -c});

        while(!pq.empty()){
            auto t=pq.top();pq.pop();
            ll len=-t.first;
            int no=-t.second;

            for(auto nav: paths[no]){
                if(dist[nav.first] > len+nav.second){
                    dist[nav.first]=len+nav.second;
                    pq.push({-dist[nav.first], -nav.first});
                }
            }
            
        }

        ll ans=0;
        int count=0;
        rep1(n, i){
            // cout<<dist[i]<<endl;
            if(dist[i]<100201020){
                ans=max(ans,(ll)dist[i]);
                count++;
            }
        }

        cout<<count<< ' ' <<ans<<'\n';
    }
}

```

</details>

