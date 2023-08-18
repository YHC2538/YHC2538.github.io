---
title: Test Blog
date: 2021-04-27 19:18:42
tags: Testing Tag
math: true
---


# 自介在 [這裡](https://yhc2538.github.io/about) 喔 ~
\
*這是一個測試用的文章* (Italic)
<!-- more -->
# Header (1)
## Header (2)
### Header (3)

分隔線測試

---

**Math** (bold)
$$\frac { dy }{ dx } =\frac { { e }^{ x } }{ 3{ y }^{ 2 } }$$


Mac syle code blocks, night theme

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
void _debug(){cerr<<'\n';}
template <typename A, typename... B>
void _debug(A a, B...b){cerr<<a<<' ', _debug(b...);}
#define debug(args...) cerr<<'('<<(#args)<<") : ", _debug(args)
const int N = 5e4+4;
int w[N], vis[N] = {}, mx = 0;
vector<int> g[N];

int dfs(int v){
    vis[v] = 1;
    int sum = 0;
    for(auto e : g[v]){
        if(vis[e]^1){
            sum += dfs(e);
        }
    }
    return sum + w[v];
}
int main(){
    int n,m; cin>>n>>m;
    for(int i = 0; i < n; i++) cin>>w[i];

    for(int i = 0; i < m; i++){
        int from, to; cin>>from>>to;
        g[from].push_back(to);
        g[to].push_back(from);
    }
    for(int i = 0; i < n; i++){
        if(vis[i]^1){
            mx = max(dfs(i), mx);
        }
    }
    cout<<mx<<'\n';
}

```



