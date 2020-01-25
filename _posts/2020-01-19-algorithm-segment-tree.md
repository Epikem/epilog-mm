---
title: "algorithm-segment-tree"
template: "post"
category: "algorithm"
tags: 
  - "algorithm"
  - "blog"
banner: "/assets/bg/2.jpg"
---

## 알고리즘 스터디 - 세그먼트 트리

### bj 12015 - 가장 긴 증가하는 부분 수열 2

https://www.acmicpc.net/problem/12015

이걸 어떻게 세그먼트 트리로 푸는지 잘 모르겠다. 이전 코드 참조해서 품.



### bj 3745 - 오름세

https://www.acmicpc.net/problem/3745

12015와 같은 문제.

<details><summary markdown="span">cpp solution</summary>

```cpp

using namespace std;

vector<int> arr;

void solve(){
    int temp;
    int N;
    while(cin>>temp){
        N=temp;


        int tmp;
        int val;
        arr.push_back(-1); // 최소값보다 작은 값을 지정
        rep(N, tmp){
            // 특정 지점에서, 현재까지 읽은 길이 중 최대값이 가장 작게 하는
            // 정보를 기억해야 함.
            // 그러려면 중간의 원소를 업데이트 할 때 효율적으로 해야 하므로
            // 이분 탐색이 필요.
            cin>>val;
            if(val>arr.back()){
                arr.push_back(val);
            } else {
                // val값이 들어갈 자리를 찾아본다.
                int left=0,right=arr.size();
                int mid;
                while(left<right){
                    mid=(left+right)/2;
                    if(arr[mid]<val){
                        left=mid+1;
                    } else if(arr[mid]>val){
                        right=mid;
                    } else if(arr[mid]==val){
                        right=mid;
                    }
                }
                arr[right]=val;
            }
        }   
        cout<<arr.size()-1<<endl;
        arr.clear();
    }
}
```

</details>



