---
title: "2020-01-05"
date: "2020-01-05"
template: "post"
category: "algorithm"
slug: "posts/2020-01-05"
tags: 
  - "algorithm"
  - "blog"
banner: "/assets/bg/1.jpg"
---

## 알고리즘 문제풀이 스터디 2020-01-05

### boj 1991 - 트리 순회

https://www.acmicpc.net/problem/1991

구조체와 map으로 트리를 구현했는데, `node*`가 아니라 `node`를 썼을 때 이상하게 동작해서 포인터로 바꾸었다. 아마 `node a`처럼 쓰려면 클래스로 해야 하는 모양이다. 문법 에러가 안 나는데 동작이 이상한데다 디버깅도 안 되는 상태여서 결국 이것 때문에 디버깅 설정을 다시 했다.


<details><summary markdown="span">cpp solution</summary>

```cpp

// https://www.acmicpc.net/problem/1991

struct node{
  node* left;
  node* right;
  char value;
};

void preorder(node* n){
  if(n->value=='.') return;
  cout<<n->value;
  preorder(n->left);
  preorder(n->right);
}

void inorder(node* n){
  if(n->value=='.') return;
  inorder(n->left);
  cout<<n->value;
  inorder(n->right);
}

void postorder(node* n){
  if(n->value=='.') return;
  postorder(n->left);
  postorder(n->right);
  cout<<n->value;
}

void solve(){
  int n;cin>>n;
  int t;
  char a,b,c;
  // std::set<char> names;
  node* root=new node();
  root->value='A';
  root->left=NULL;
  root->right=NULL;
  map<char,node*> nodes;
  nodes.insert({'A', root});

  rep(n, t){
    cin>>a>>b>>c;
    // cout<<"start\n";
    // cout << a << ' '<< b << ' ' << c << '\n';
    node* cur=nodes.at(a);
    //node* cur2=nodes.find(a);
    // cout << cur->value << endl;
    // if(b!='.'){
      node* leftchild = new node();
      leftchild->left=NULL;
      leftchild->right=NULL;
      nodes.insert({b, leftchild});
      leftchild->value=b;
      cur->left=leftchild;
    // }
    // if(c!='.'){
      node* rightchild = new node();
      rightchild->left=NULL;
      rightchild->right=NULL;
      nodes.insert({c, rightchild});
      rightchild->value=c;
      cur->right=rightchild;
    // }
    // cout<<"printing all\n";
    // for(int i=0;i<nodes.size();i++){
    //   // cout << nodes.at((i+'A')) << endl;
    //   cout << nodes.find(i+'A')->second->value << endl;
    // }
    // cout<<"end\n";
  }

  preorder(root);
  cout<<endl;
  inorder(root);
  cout<<endl;
  postorder(root);

}

```

</details>



### boj 1269 - 대칭 차집합

https://www.acmicpc.net/problem/1269


<details><summary markdown="span">cpp solution</summary>

투 포인터 문제. 정렬 안 해서 1미스
정렬 해놓고 작은 쪽의 인덱스를 증가시켜가면서 같은 값이 나온 개수를 세고, 전체 개수에서 같은 값 나온 개수 두배만큼 빼면 된다.

```cpp

using namespace std;

// https://www.acmicpc.net/problem/1269
vi a;
vi b;
void solve(){
  
  int A,B;cin>>A>>B;
  int tmp;
  for(int i=0;i<A;i++){ 
    cin>>tmp;
    a.push_back(tmp);
  }
  for(int i=0;i<B;i++){
    cin>>tmp;
    b.push_back(tmp);
  }
  // 원소 값이 상당히 크므로 값으로 인덱싱해서 비교할 수는 없다.
  // 개수가 20만개 정도인데 시간 제한이 2초라면 퀵정렬 정도는 할 수 있을듯?
  // 문제는 트리 그룹 문제라 트리를 써야하는가..
  // 보기에는 투포인터 문제같은데.
  int dup=0;
  int i=0,j=0;
  sort(all(a));
  sort(all(b));
  while(i<A && j<B){
    if(a[i]==b[j]) {
      i++;j++;dup++;
    } else {
      if(a[i]>b[j]) j++;
      else if(a[i]<b[j]) i++;
    }
  }
  int ans=a.size()+b.size()-dup*2;
  cout<<ans<<endl;
}

```
</details>



### boj 11725 - 트리의 부모 찾기


<details><summary markdown="span">cpp solution</summary>

트리 구현 없이 될까 싶었는데, 부모를 찾는 것이므로 자식을 여러번 순회할 필요 없고, 각 정점 번호로 주어지므로 값 인덱싱을 사용할 수 있어 dfs로 가능했다.

```cpp

using namespace std;

// 트리의 부모 찾기
// https://www.acmicpc.net/problem/11725

// 트리를 구현 안하고 풀 수도 있을 것 같다.
// 어차피 사이클이 없으니, 인접 배열을 만든 다음 
// 루트만 지정해서 bfs 또는 dfs를 하면서,
// 레벨을 비교하면 되지 않을까?

// 문제는, 인접 행렬을 쓰기엔 너무 개수가 많고, (10만x10만)
// 인접 배열을 쓰기엔 이진 트리가 아니기 때문에 
// 순회가 곤란하다. 값이 명시적으로 제한되어있지 않지만 노드 번호이므로 
// 값 인덱싱을 쓸 수 있을지도 모른다.

vi arr[100005];
int parents[100005];
bool visited[100005];

void dfs(int parent){
  for(auto adj: arr[parent]){
    if(visited[adj]) continue;
    visited[adj]=true;
    parents[adj]=parent;
    dfs(adj);
  }
}

void solve(){
  int N;
  int t;
  int a,b;
  cin>>N;
  rep(N,t){
    cin>>a>>b;
    arr[a].push_back(b);
    arr[b].push_back(a);
    visited[t]=false;
  }

  visited[1]=true;
  dfs(1);

  for(int i=2;i<=N;i++){
    cout << parents[i]<<'\n';
  }

}

```

</details>




### boj 1717 - 집합의 표현

<details><summary markdown="span">cpp solution</summary>

기본 Union-Find 구현.
배열이 백만개까지도 돌아간다.

```cpp

using namespace std;

// 집합의 표현
// https://www.acmicpc.net/problem/1717

int arr[1000005];

int getRoot(int a){
  if(arr[a]==a) return a;
  // path compression
  auto ret=getRoot(arr[a]);
  arr[a]=ret;
  return ret;
}

void merge(int a, int b){
  int roota=getRoot(a);
  int rootb=getRoot(b);
  arr[rootb]=roota;
}

bool check(int a, int b){
  return getRoot(a) == getRoot(b);
}

void solve(){
  int N,M;cin>>N>>M;
  int a,b,t,type;
  rep(1000002,t){
    arr[t]=t; 
  }
  rep(M,t){
    cin>>type>>a>>b;
    if(type==0){
      merge(a,b);
    } else {
      if(check(a,b)){
        cout<<"YES"<<'\n';
      } else cout<<"NO\n";
    }
    
  }
}

```

</details>





### boj 16562 - 친구비

<details><summary markdown="span">cpp solution</summary>

```cpp

using namespace std;

// 친구비
// https://www.acmicpc.net/problem/16562

int arr[10005];
int dist[10005];

int getRoot(int a){
  if(arr[a]==a) return a;
  // path compression
  auto ret=getRoot(arr[a]);
  arr[a]=ret;
  return ret;
}

int getDist(int a){
  if(arr[a]==a) return dist[a];
  // path compression
  auto ret=min(getDist(arr[a]), dist[a]);
  dist[a]=ret;
  return ret;
}

void merge(int a, int b){
  int roota=getRoot(a);
  int rootb=getRoot(b);
  dist[roota]=min<int>(getDist(b), getDist(a));
  dist[rootb]=min<int>(getDist(a), getDist(b));
  arr[rootb]=roota;
}

bool check(int a, int b){
  return getRoot(a) == getRoot(b);
}

void solve(){
  int N,M,K;
  cin>>N>>M>>K;
  int tt,v,w,tmp;
  rep1(N,tt){
    arr[tt]=tt;
    cin>>dist[tt];
  }
  rep(M,tt){
    cin>>v>>w;
    merge(v,w);
  }
  // 최소비용: 준석이 set로부터 시작해서 모든 set를 합칠때까지 최소 비용
  // 그런데, 합쳐진 set이 다른 set과 연결되어 비용이 낮아지는 경우도 있다.
  // 그러면, 모든 set간의 최소 비용들을 일단 기준으로 하면 될 것이다.
  // set간의 최소가 아닌 비용은 고려할 필요가 없을 것 같다.
  // 그러면 set 정보를 기반으로 그래프를 구축해야 하나?
  // 그래프 구축할 때 set간의 최소 비용은 어떻게 찾지?
  // 아예 학생간 거리를 써서 merge할 때 그 정보도 포함해가며 최소거리 정보를 
  // 유지한다면 될 거 같다.
  // 그러면 그래프 구축은 어떻게?
  // 루트들을 중심으로 구축하면 될 거 같다.
  // 루트들을 얻으려면 getRoot와 set를 쓰거나, getRoot해서 자기자신이 나오는 노드만
  // 리스트에 넣어놓고 정점 리스트로 쓰면 된다.
  // 그러면, 전체 최소 비용은 결국 어떻게 구하나?
  // 잠깐 이거 탐색 문제가 아닌게, 어차피 준석 기준으로만 다른 친구들과 연결해야 하고 
  // 다른 친구들끼리 연결하는건 merge과정에서만 일어난다!
  // 따라서 그냥 준석 set가 아닌 모든 그룹까지의 최소 거리들을 구하면 된다.
  ll ans=0;
  for(int t=1;t<=N;t++){
    if(t==getRoot(t)){
      // 루트들에 대해
      // 준석이가 따로 그룹이 있는 것이 아니므로(아싸ㅠㅠ) 모든 루트들을 매수하면 된다.
      ans+=getDist(t);
    }
  }
  if(ans>K){
    cout << "Oh no\n";
  } else cout<<ans<<endl;

}


```

</details>




### boj 4803 - 트리

<details><summary markdown="span">cpp solution</summary>

합칠 때 사이클 플래그도 루트들에 새로 설정해주지 않아서 1미스

```cpp
using namespace std;

// 트리
// https://www.acmicpc.net/problem/4803

int arr[1005];
int dist[1005];
bool hasCycle[1005];

int getRoot(int a){
  if(arr[a]==a) return a;
  // path compression
  auto ret=getRoot(arr[a]);
  arr[a]=ret;
  return ret;
}

int getDist(int a){
  if(arr[a]==a) return dist[a];
  // path compression
  auto ret=min(getDist(arr[a]), dist[a]);
  dist[a]=ret;
  return ret;
}

void merge(int a, int b){
  int roota=getRoot(a);
  int rootb=getRoot(b);
  if(roota==rootb){
    hasCycle[a]=true;
    hasCycle[b]=true;
    hasCycle[roota]=true;
  }
  if(hasCycle[roota] || hasCycle[rootb]){ // 이 부분 빠지면 틀림.
    hasCycle[roota]=true;
    hasCycle[rootb]=true;
  }
  dist[roota]=min<int>(getDist(b), getDist(a));
  dist[rootb]=min<int>(getDist(a), getDist(b));
  arr[rootb]=roota;
}

bool check(int a, int b){
  return getRoot(a) == getRoot(b);
}

void solve(){
  int N=1,M=1;
  cin>>N>>M;
  int testcase=1;
  while(N!=0 || M!=0){
    int tt,v,w,tmp;
    rep1(N,tt){
      arr[tt]=tt;
      hasCycle[tt]=false;
    }
    rep(M,tt){
      cin>>v>>w;
      merge(v,w);
    }

    int ans=0;
    for(int t=1;t<=N;t++){
      if(t==getRoot(t) && !hasCycle[t]){
        ans++;
      }
    }
    cout<<"Case " << testcase << ": ";
    if(ans==0){
      cout<<"No trees.\n";
    }else if(ans==1){
      cout << "There is one tree.\n";
    } else {
      cout << "A forest of " << ans << " trees.\n";
    }
    cin>>N>>M;
    testcase++;
  }
}
```
</details>

