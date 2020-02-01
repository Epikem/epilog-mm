---
category: "algorithm"
tags: 
  - "algorithm"
  - "blog"
---

## 백준 알고리즘 스터디 - 투 포인터 및 슬라이딩 윈도우 2


### 백준 1806 - 부분합

https://www.acmicpc.net/problem/1806

c++의 `deque`가 앞,뒤 원소 조작이 $O(1)$이라는 건 당연하지만, 랜덤 액세스에 대해서도 $O(1)$이라는 건 신기한 거 같다. 

문제 대충 봐서 3미스, 출력 주석처리 안해서 2미스

부분합 S 이상에 대해 최소길이를 구하는건데 부분합 S일때만 고려하게 했었다. 이걸 찾기 전까지는 i 읽을때의 문제라고 생각했었음. 마지막 원소 읽고 나서도 lo를 늘리다가 새 답을 찾을 수 있으니 계속 돌려야 하는건가? 하고 착각했다.
그런데 그게 아닌 것이, 투 포인터 문제에서 마지막 원소 다음 원소를 읽으려 한다면 이미 탐색 실패이므로 바로 나가야 한다.

<details><summary markdown="span">cpp solution</summary>

```cpp

using namespace std;

void solve(){
    deque<int> arr;
    // rep(arr.size(), i){
    //     cout<< arr[i] << ' ';
    // }

    int N,S;
    cin>>N>>S;
    int lo,hi,a,b,i,j;
    int curlen=0;
    lo=0;hi=0;
    int ans=MAXX;
    int item;
    // 하나 놓고 시작해서, 새로 가져올 때마다
    // 모자란 동안 hi를 더하고, 넘치는 동안 lo를 더하는 식으로?
    // 
    int cursum=0;
    bool inc=false, dec=false;
    i=0;
    while(true){
        if(cursum<S){
            inc=true;
        } else if(cursum>S){
            dec=true;
            ans=min(ans, curlen); // 이 줄 없으면 틀림.
        } else {
            ans=min(ans, curlen);
            inc=true, dec=true;
        }
        if(inc){
            // cout<<i<<'\n';
            if(i<N){
                cin>>item;
            } else break;
            i++;
            arr.push_back(item);
            curlen++;
            cursum+=item;
        }
        if(dec){
            cursum-=arr.front();
            arr.pop_front();
            curlen--;
        }
        inc=false; dec=false;
    }
    if(ans==MAXX){
        cout<<0<<endl;
    } else {
        cout<<ans<<endl;
    }

}
```

</details>