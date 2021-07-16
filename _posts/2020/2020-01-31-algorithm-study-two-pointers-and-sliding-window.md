---
category: "algorithm"
tags: 
  - "algorithm"
  - "blog"
last_modified_at: 2020-02-01
---

## 백준 알고리즘 스터디 - 투 포인터 및 슬라이딩 윈도우

### bj 2003 - 수들의 합 2

https://www.acmicpc.net/problem/2003

쉬운 투포인터 문제

<details><summary markdown="span">cpp solution</summary>

```cpp
#include <iostream>
#include <vector>
#include <math.h>
using namespace std;
#define ll long long
#define vi vector<int>
 //Compiler version g++ 6.3.0

 int main()
 {
 	int n,m,tmp;
 	
 	vector<int> arr;
 	cin>>n>>m;
 	
 	int a=0,b=0,cursum=arr[0],ans=0;
 	for(int i=0;i<n;i++){
 		cin>>tmp;
 		arr.push_back(tmp);
 		
 	}
 	cursum=arr[0];
 	while(true){
 		if(cursum<m){
 			b++;
 			if(b>n){
 			break;
 		}
 			cursum+=arr[b];
 		}else if(cursum==m){
 			ans++;
 			cursum-=arr[a];
 			a++;b++;
 			if(b>n){
 			break;
 		}
 			cursum+=arr[b];
 			
 		}else {
 			cursum-=arr[a];
 			a++;
 		}
 	}
 	cout<<ans;
 }

```

</details>


### bj 2230 - 수 고르기

https://www.acmicpc.net/problem/2230

런타임 에러로 6미스. 정렬 후 중복제거를 했는데, 배열의 원소개수 n을 그대로 사용해서 문제가 되었다. 중복제거한 배열 기준으로 n을 업데이트하니 성공.


<details><summary markdown="span">cpp solution</summary>

```cpp
#include <iostream>
#include <vector>
#include <math.h>
#include <algorithm>
using namespace std;
#define ll long long
#define vi vector<int>
#define pb push_back
 //Compiler version g++ 6.3.0

 int main()
 {
 	ll n,m,tmp;
 	
 	vector<int> arr;
 	vi arr2;
 	cin>>n>>m;
 	
 	int a=0,b=0;
 	ll cursum=arr[0],ans=2000000005;
 	for(int i=0;i<n;i++){
 		cin>>tmp;
 		arr.push_back(tmp);
 		
 	}
 	sort(arr.begin(), arr.end());
 	arr2.pb(arr[0]);
 	if(n>0)
 	for(int i=1;i<n;i++){
 		if(arr[i]!=arr[i-1]){
 			arr2.pb(arr[i]);
 			//cout<<arr[i];
 		}
 		
 	}
 	n=arr2.size();
 	
 	ll curm=arr2[b] - arr2[a];
 	while(a<n && b<n){
 		curm=arr2[b] - arr2[a];
 		//cout<<curm << " ";
 		if(curm<=m){
 			if(curm==m){
 				
 				cout<<m<<endl;
 				return 0;
 			}
 			b++;
 			if(b>=n){
 				break;
 			}
 		} else{
 			a++;
 			ans=min<ll>(ans, curm);
 		}
 	}
 	cout<<ans<<endl;
 	
 	return 0;
 }
```

</details>


### bj 1484 - 다이어트

범위조정 문제로 5미스. 비효율적인 방법으로 품.

<details><summary markdown="span">cpp solution</summary>

g의 최대가 10만인데, 두 자연수 제곱 차는 $n^2 - (n-1)^2 = 2n - 1$이므로 n은 최대 5만정도이다. 그러면 현재 몸무게 n과 이전 몸무게 k에 대해 대략 $O(n^2)$이 되는데, n이 5만이면 시간초과 가능성이 높다.
그래서 어떤 n에 대해 g의 범위는 $n^2$ 미만 $2n-1$ 이상이므로, 이를 이용하여 스캔 범위를 줄여서 풀었다.
그런데 400ms정도 나왔는데, 다른 제출기록 보니 0ms도 있다. 어떻게 가능한거지?
맞추고 나서 태그를 보니 투 포인터라고 되어 있다.. n과 k의 투 포인터를 쓴다? 하지만 정렬된 일차원 배열이 아니고, 이차원인데다 g값들이 겹치는 경우가 존재하는데 어떻게?? 
그리고 투포인터는 조건에 맞는 값을 찾는 위치들을 찾을때 쓰던데.. 지금도 그런 경우긴 하지만

```cpp
const int MAXX=1000000005;

using namespace std;


void solve(){
    int G;
    cin>>G;
    double Gf=(double)G;
    vector<int> arr;
    double past=1.0f;
    double now=1.0f;
    int nown=1;
    now=sqrt(Gf);
    nown=floor(now);
    // cout<<nown<<endl;
    while(nown*2-1<=G){
        int sumv=0;
        for (int past = nown*2-1; past > 0; past-=2)
        {
            sumv+=past;
            if(sumv==G && nown*nown!=G){
                // cout<<sumv<<endl;
                arr.push_back(nown);
                break;
            }
        }
        nown++;
    }
    if(arr.size()==0) {
        cout<<-1<<endl;
        return;
    }
    for (int i = 0; i < arr.size(); i++)
    {
        cout << arr[i] << '\n';
    }
}

```

영 궁금해서 [다른 블로그](http://joonas-yoon.blogspot.com/2016/03/1484.html)에 있는 이진 탐색 풀이를 가져와 돌려보니 8ms가 나왔다. 하고 안하고에 따라 400ms, 8ms이니 차이가 크긴 한데 여전히 0ms는 어떻게 가능한건지 모르겠다.

[또다른 블로그](https://baelanche.tistory.com/249)에서 해답을 찾았다. 역시 투포인터로 해야 가능한 것이었다. 차이를 계산하는 두 비교대상인 몸무게가 가리키는 것은 자연수로 비교 가능한 대상이다. 생각한 현재 몸무게가 늘 수록, 이전 몸무게가 줄 수록 제곱 차이 G는 커지고, 반대는 작아진다. 이걸 왜 못찾았지? 게다가 현재 몸무게 자체를 포인터로 두면서 더하면서 찾으면 sqrt같은 걸 쓸 필요도 없어진다.

투포인터를 강제하려면 c++은 제한시간 0.1초를 줘야 할 지도.

투포인터로 재시도. 아이디어를 알고도 시작조건과 종료조건 문제로 8번 틀림(한숨).

```cpp
const int MAXX=1000000005;

using namespace std;


void solve(){
    long G;
    cin>>G;
    long i,j,k,tmp;
    long lo=1,hi=2; // 몸무게이고, 증가량 G가 자연수이므로 시작조건은 최소 이전 몸무게 1, 현재 몸무게 2부터 시작해야 한다.
    // hi hi - lo lo = G
    long diff=hi*hi-lo*lo;
    bool found=false;
    while(true){    // diff<=100005 이런 식으로 하게 되면, hi-lo가 커서 20만 차이 나지만, lo를 늘리면 다시 다른 G를 찾을 수 있는데도 종료되게 되서 틀린다.
        if(diff>100000 && hi-lo==1) break;
        // diff가 G와 같다면 -> 둘 다 1씩 늘림
        // diff가 G보다 작다면: hi를 늘림
        // diff가 G보다 크다면: lo를 늘림
        if(diff==G){
            cout<<hi<<'\n';
            found=true;
            lo++;hi++;
        } else if(diff<G){
            hi++;
        } else {
            lo++;
        }
        diff=hi*hi-lo*lo;
    }
    if(!found){
        cout<<-1<<endl;
    }
}
```




</details>


### bj 2096 - 내려가기

https://www.acmicpc.net/problem/2096

왼쪽 아래, 아래, 오른쪽 아래로 내려갈 수 있을 때 가능한 최대 점수/최소 점수 계산하기

분명 간단하게 표현할 수 있을거 같은데 정보를 어떻게 어떤 식으로 저장해야 할 지 헷갈렸다.
처음엔 dfs를 구현해서 메모이제이션 하려고 했는데 분명 더해 내려가면 이렇게 구현할 필요가 없을 거란 생각이 들어서 더해 내려가도록 구현했다.


<details><summary markdown="span">cpp solution</summary>

한 칸이 그 칸까지의 최소합, 최대합을 저장해야 하므로 배열이 두 개가 필요하다. 거기에 현재 계산중인 최대/최소 합을 저장해놓을 공간이 필요하므로 배열 네 개를 사용했다.

지금 보니 1칸보다 더 이전의 정보에 접근하지 않으므로 10만짜리 배열은 필요가 없었다. 그냥 세칸짜리 배열 만들어서 매 번 새로 받으면 된다.

```cpp

const int MAXX=1000000005;

using namespace std;

int arr[100005][3];
int prevmins[3];
int prevmaxs[3];
int mins[3];
int maxs[3];
const int dr[]={-1,0,1};

bool check(int p){
    if(p<0 || p>=3) return false;
    return true;
}

void solve(){
    int N,i,j;
    cin>>N;
    rep1(N, i){
        cin>>arr[i][0]>>arr[i][1]>>arr[i][2];
    }

    for (int i = 0; i < 3; i++)
    {
        prevmins[i]=0;
        prevmaxs[i]=0;
    }
    
    // max_arr_n_p=max(arr_n-1_p(0,1,2))+arr_n_p
    // min_arr_n_p=min(arr_n-1_p(0,1,2))+arr_n_p

    // 시작: (0,0), (0,1), (0,2)
    // 각 단계: 가능한 위 수들을 더해서 높은/낮은 수 기억.
    int maxv=0;
    int minv=1000000008;
    rep1(N, i){
        memset(mins, MAXX, 3*sizeof(int));
        memset(maxs, 0, 3*sizeof(int));
        for (int j = 0; j < 3; j++)
        {
            for (int dir = 0; dir < 3; dir++)
            {
                int p=j+dr[dir];
                if(!check(p))continue;
                maxs[j]=max(maxs[j], prevmaxs[p]+arr[i][j]);
                mins[j]=min(mins[j], prevmins[p]+arr[i][j]);
            }
        }
        memcpy(prevmins, mins, 3*sizeof(int));
        memcpy(prevmaxs, maxs, 3*sizeof(int));
    }
    for (int i = 0; i < 3; i++)
    {
        maxv=max(maxv, maxs[i]);
        minv=min(minv, mins[i]);
    }
    cout<<maxv<< ' ' <<minv<<endl;

}
```

</details>




### bj 3109 - 빵집

https://www.acmicpc.net/problem/3109

잘못 생각해서 1미스, 주석처리 안해서 1미스

<details><summary markdown="span">cpp solution</summary>

dfs나 bfs 탐색으로 하면 시간초과날거같다는 생각을 했는데, 메모이제이션을 하면 한번씩만 훑는거나 다름없어 500만정도의 비용이므로 충분히 1초안에 돌릴 수 있다.

```cpp
char maps[10005][505];
// bool prevCols[505];
// bool rows[505];
bool cache[10005][505];
bool visited[10005][505];

const int dr[]={-1,0,1};

int R,C;
bool dfs(int c, int rr){
    if(visited[rr][c]) return cache[rr][c];
    visited[rr][c]=true;
    bool& ret=cache[rr][c];
    if(c==C) {
        ret=true;
        return ret;
    }

    int i=0;
    rep(3, i){
        int nr=rr+dr[i];
        if(visited[nr][c+1]) continue;
        // visited[nr][c+1]=true;
        if(maps[nr][c+1]!='x')
            if(dfs(c+1,nr)){
                ret=true;
                maps[nr][c+1]='y';
                return ret;
            }
    }
    ret=false;
    return ret;
}

void solve(){
    int rr,cc;
    cin>>R>>C;
    string ss;
    reprange(0,C+3,cc){
        reprange(0,R+3,rr){
            maps[rr][cc]='x';
        }
    }
    rep1(R,rr){
        cin>>ss;
        rep1(C,cc){
            maps[rr][cc]=ss[cc-1];
            cout<<maps[rr][cc]<< ' ';
        }
        cout<<'\n';
        // rows[rr]=true;
    }

    cout<<'\n';

    int ans=0;
    rep1(R,rr){
        if(dfs(0, rr)){
            ans++;
        }
    }

    // rep1(R,rr){
    //     rep1(C,cc){
    //         cout<<maps[rr][cc]<< ' ';
    //     }
    //     cout<<'\n';
    // }

    cout<<ans<<endl;

}

```
</details>