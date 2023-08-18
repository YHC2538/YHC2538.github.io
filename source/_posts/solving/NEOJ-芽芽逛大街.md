---
title: NEOJ | 芽芽逛大街
date: 2021-06-03 11:48:18
tags:
- Graph
- DP
- SCC
- 3-STAR
- Topological
math: true
categories:
- [解題歷程]
---

***Difficulty:***

:star::star::star:

:star2: 滿喜歡這題, 給他 special 星

## Problem Description 題敘:

[Link](https://neoj.sprout.tw/problem/739/)

給一個單向圖，有 $N$ 條邊，以及 $M$ 個點，每造訪一個點有 $\large s_i \in \mathbb{N}$ 的美麗值，每造訪一個道路也有  $\large w_{u \longrightarrow v} \in\mathbb{N}$ 的美麗值。

可任選一個景點出發並停留在任意可以抵達的點，若採取最佳的策略，求可以得到總和多大的美麗值。

請注意，重複造訪某個已經到過的點或道路並不會得到額外的任何美麗值。


<br/>

$N,M(1\le N\le 5\times 10^5,0\le M\le 5\times 10^5)$

$s_1,\cdots,s_N(0\le s_i\le 10^9)$

$u_i,v_i,w_i(1\le u_i,v_i\le N,u_i\ne v_i,0\le w_i\le 10^9)$

---

<!-- more -->

## Thinking 思考歷程:

 因為是單向圖，有可能走到某個錯的點就走不回來了。又發現，同一個區塊的點可以亂走，題目也沒規定不能經過重複的點，圖也可能有環，所以要在有環的圖求最長路徑....



## Solution 題解:

想法其實很簡單，可以 SCC! , 把他縮點後會是一個 DAG，之後再做 DP 就好了 !

討論一些細節:

### SCC:

首先我們要找 SCC，這個可以直接套 Kosaraju 的演算法會比較簡單。在第二次 DFS 時 (reverse dfs) ，我們可以順便記錄一些 之後要找最長路徑的訊息。我用 `sccw[]` (scc weight) 這個變數來記錄這個 SCC 點的總權重，接著 Kosaraju 算法的第二次 DFS 中， 拜訪到點 $v$ 時，把 `scc[v] = id` ，再把點 $v$ 本身的權重加進 SCC，然後跟據 Kosaraju，所找到的點都跟 $v$ 是同一個 SCC，因此，可以把  $w_{v\longrightarrow v}$ 這條邊權加進去 `sccw[]` 。

那因為 `if(scc[u] == 0)` 這個是特判這個點是否已經從圖中拔除，若該點已經在一個 SCC 裡面就不要再重複拜訪。但如果只這樣判 會出現一個問題，題目沒有說不會有 $u \longrightarrow v$ 跟 $u \longleftarrow v$ 這種邊存在。即  若  $v$ 拜訪到 $u$ ，則 $\large w_{v \longrightarrow u}$ 會被加進去 `sccw[]` 裡面，且 $u$ 一定不會再拜訪 $u$ 。那麼 若 $u \longrightarrow v$ 有邊，則 $\large w_{u \longrightarrow v}$ 一就不會被加進 `sccw[]` 裡面了。

據此，對於那些已經拜訪過的並從圖中移除的點，這裡要多一個特判。若 $u$ 的 `id` 跟 當前 $v$ 的 `id` 一樣  (`scc[u] == id`) 則這條邊的值也把他加進去 `sccw[]`。

### 重新建圖

現在已經有新的 SCC 點，那為了能在新點上做 topological + dp，必須建一張新的圖。這就簡單啦! 利用我們的舊圖來建新圖，每個點都掃過去一次，每掃到一個點，就去檢查他的鄰居，若他們屬於不同的 SCC 點，就為這兩個 SCC 點建邊。

這裡想像一下，點找到自己所屬的 SCC 點時，就像點被 **放大了** ，跟其他也被放大的點建邊。

如圖:

![](https://i.imgur.com/iM7Cux0.png)



### Topological + DP

(* 以下所述的 "點" 為縮點後的 SCC 點) 

縮點後一定是 DAG，因此就可以 找 Toplogical order 再做 dp。`dis[]` 是我們的距離 dp 陣列。 這個地方因為 點有權重，邊也有權重。因此為了處理上的方便，可以先把 入度為 0 的點的權重先加進去 `dis[]` 裡面。

dp 的轉移也很簡單:
$$
\large dis[u] = \max\\{dis[v] + w(u\longrightarrow v) + sccw[u]\\}, for\ all\ (v\rightarrow u) \in G_{scc}
$$
接著 `ans` 可以順便取 $\max$。 其他的應該跟一般的 dag on DP 一樣 ~



## AC Code 程式碼:   

```cpp
#include <bits/stdc++.h>
typedef long long LL;
using namespace std;
const int N = 5e5+5;
vector<pair<int,int>> g[N], rg[N], sccg[N]; //graph, reversed, shrinked
bool visited[N]; 
LL scc[N], sccw[N], dis[N] = {}, indeg[N] = {}, weight[N]; //scc of [v_4] = scc_id; sccw[scc_id] = weight.
vector<int> order; //order 為離開戳記 小到大

void dfs(int x){
    visited[x] = 1;
    for(auto e : g[x]){
        int u = e.first;
        if(visited[u]^1){
            dfs(u);
        }
    }
    order.push_back(x);
}

void rdfs(int x, int id){
    scc[x] = id;
    sccw[id] += weight[x]; 
    for(auto e : rg[x]){
        int u = e.first;
        if(scc[u] == 0){
            rdfs(u, id);
            sccw[id] += e.second;
        }
        else if (scc[u] == id){
            sccw[id] += e.second;
        }
    }
}

int main(){ 
    int n,m; cin>>n>>m;
    for(int i = 1; i <= n; ++i) cin>>weight[i];
    for(int i = 1; i <= m; i++){
        int u,v,w; cin>>u>>v>>w;
        g[u].push_back({v, w});
        rg[v].push_back({u, w});
    }
    for(int i = 1; i <= n; i++){ // 圖可能不連通，都要 dfs 看看
        if(!visited[i]) dfs(i);
    }
    int sccid = 1; 
    
    // larger 離開戳記先 dfs
    for(int i = n-1; i >= 0; --i){ 
        int x = order[i];
        if(!scc[x]){ //還未縮點
            rdfs(x, sccid);
            sccid++;
        }
    }

    //重新建圖
    for(int i = 1; i <= n; i++){
        for(auto e : g[i]){
            int scc1 = scc[i];
            int scc2 = scc[e.first];
            if(scc1 != scc2){ // 不同的點的話，就要建邊
                sccg[scc1].push_back({scc2, e.second});
                indeg[scc2]++;
            }
        }
    }
    
    // 縮點完成後，會是一個 DAG. 可以找 topo order + DP
    queue<int> topo; 
    for(int i = 1; i < sccid; i++){
        if(indeg[i] == 0) { 
            topo.push(i);
            dis[i] += sccw[i]; //initial points
        } 
    }
    LL ans = 0;
    while(topo.empty()^1){
        LL v = topo.front();
        topo.pop();
        for(auto e : sccg[v]){
            LL u = e.first;
            LL w = e.second;
            
            // dp
            dis[u] = max(dis[u], dis[v] + sccw[u] + w);
            ans = max(ans, dis[u]);
            if(--indeg[u] == 0) topo.push(u);
        }
    }
    cout<<ans<<'\n';
}
```



<br/>

### 學習到的新技巧 or 概念 ~

- Kosaraju Algorithm
- DAG on DP
