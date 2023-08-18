---
title: TCIRC-d086 | 矩陣乘法鏈
date: 2021-05-02 18:04:18
tags:
- DP
- 3-STAR
- Classic
- ZeroJudge
- TCIRC
math: true

---


***Difficulty:***

:star::star::star:

## Problem Description 題敘:

[Link](https://judge.tcirc.tw/ShowProblem?problemid=d086)

[ZJ 也有一樣的題目](https://zerojudge.tw/ShowProblem?problemid=d652) (不過還是以矩陣這題來說明)

兩個矩陣 A 與 B 要相乘，必須滿足 A 的行數等於 B 的列數，若 A 是 p * q 的矩陣，B

是 q * r 的矩陣，則 A * B 的結果是一個 p*r 的矩陣，而需要的純量乘法數假設是 p * q * r 。

矩陣乘法滿足結合律，也就是說 A * B * C = (A * B) * C = A * (B * C)，可是不同順序所需要的純量乘法數不同。

因此請找出最好的相乘順序使得使用的純量乘法數的總和最少。

---

<!-- more -->

## Thinking 思考歷程:

 嗯，沒有明確計算順序耶，好難想 ... 狀態也列不太出來，燒雞。只知道直接假設 `dp[][]` 為所需的乘法次數就沒了。沒辦法了，舉白旗，於是去看了一下題解，後來發現題目原來只要求 $O(n^3)$ 的解就好，那是不是該枚舉甚麼呢?  如果 $dp$ 沒有名顯計算順序可以直接轉移過來的話，那應該可以跟分治一樣，讓我**當前的區間**是由**兩個較小區間**相乘後轉移過來的? 似乎是可以這麼做?

## Solution 題解:

此題是區間 DP 經典題。個人有那種類似分治的 "感覺" (只不過在這裡沒有平均切成兩半啦!) ，先假設我們要算的是 `dp[L][R]`， (這裡採左閉右閉，$L$ 跟 $R$ 各代表給定序列中其中一個數字的索引，例: `[3][4]` 及是一個矩陣)，接著可以枚舉一個 $K$ ，始得:
$$
dp[L][R] = min(dp[L][R],\ dp[L][K] + dp[K][R] + 兩個較小矩陣相乘的\ cost), \ \ \forall\ K\in L\sim R-1
$$
寫完了? 但等等 ! 對於那兩個矩陣的 $cost$ 怎麼辦?? 該不會還是要一個一個乘起來吧? 這樣複雜度挺爛的耶... 那應該不太需要這麼做。根據題目，我們不能改變矩陣序列的順序，只能改變相乘的順序，因此 $cost$ 其實就是:
$$
矩陣_L\ 的左端 \times 矩陣_K\ 的右端\ (or\ 矩陣_{K+1}\ 的左端) \times\ 矩陣_R\ 的右端
$$
OK! 那接下來就是要處理的部分，就是找 $Bottom\ up$ 的計算順序。不過對於這題我真心較喜歡  $Top\ down$ ，因為在這題的它更直覺，且好寫多了 (底下也有 top down Code)。 那 ，$Bottom\  up$ 順序又是怎麼做的呢? 回顧一下，我們用了 $dp[L][R]$ 來假設我們已經擁有了某個區間，接著才枚舉 $K$ 的位置。那我們回過頭來想，這個 "區間" 又要怎麼訂出來呢? 肯定也是枚舉 $L$ 跟 $R$ 啊! 但且慢，仔細想一下，如果依照一般的二維陣列計算順序會吃到還沒計算過的值喔，那又該怎麼辦? 想想剛剛的計算順序為何會出問題。
一個可行個想法是枚舉**長度**，也就是沒舉所有 $2$ 個相鄰矩陣相乘的結果，接著枚舉所有 $3$ 個相鄰矩陣相乘的結果，再枚舉所有 $4$ 個相鄰矩陣相乘的結果.... 一直做到 $n$ ，也枚舉所有 $n-1$ 個相鄰矩陣相乘的結果。

一旦有了區間的長度，就能枚舉 $L$ 跟$R$ 了! 如此就不會有提取沒算過值的情形囉!

## AC Code 程式碼:   

其實這幾份 code 幾乎一樣。

**TCIRC-d086 | Bottom Up:**

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 2e2+5;
int dp[N][N] = {}, cost[N];
int main(){
    int n; cin>>n; // [0, n)
    n++;
    for(int i = 0; i<n; i++) cin>>cost[i];
    for(int len = 3; len <= n; len++){ //start from 兩個矩陣。(三個數字)
        for(int l = 0; l+len-1<n; l++){ //其實 left 的上屆範圍就是界定 r .
            //0 1 2 3 4 | 5
            //    ^   ^  
            int r = l+len-1; //最後面的 r 其實就是到
            dp[l][r] = 2e9; 
            for(int k = l+1; k<r; k++){ //枚舉 k ->[l+1, r-1], 一樣左閉右閉
                dp[l][r] = min(dp[l][r], dp[l][k] + dp[k][r] + cost[l] * cost[k] * cost[r]);
            }
        }
    }
    cout<<dp[0][n-1]<<'\n';
}
```



**ZJ-d652 | Top Down: ([參考資料](http://slides.com/w86763777/deck-7/fullscreen#/2/5))**

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 2e2+5;
int dp[N][N], cost[N];

int DP(int l, int r){
    // 左閉右閉
    if(dp[l][r] != -1) return dp[l][r];
    if(r-l == 1) {  
        dp[l][r] = 0;
        return dp[l][r];
    }
    dp[l][r] = 2e9;
    for(int k = l+1; k<r; k++){
        dp[l][r] = min(dp[l][r], DP(l, k) + DP(k, r) + cost[l] * cost[k] * cost[r]);
    }
    return dp[l][r];
}
int main(){
    int n; cin>>n; for(int i = 0; i < n; i++) cin>>cost[i];
    memset(dp, -1, sizeof(dp));
    cout<< DP(0, n-1)<<'\n';
}
```

**ZJ-d652 | Bottom Up:**

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 2e2+5;
int dp[N][N] = {}, cost[N];
int main(){
    int n; cin>>n; // [0, n-1]
    for(int i = 0; i<n; i++) cin>>cost[i];
    for(int len = 3; len <= n; len++){ //start from 兩個矩陣。(三個數字)。枚舉長度
        for(int l = 0; l+len-1<n; l++){ //其實 left 的上屆範圍就是界定 r .
            //0 1 2 3 4 | 5
            //    ^   ^  
            int r = l+len-1; //最後面的 r 其實就是到
            dp[l][r] = 2e9;
            for(int k = l+1; k<r; k++){ //枚舉 k ->[l+1, r-1], 一樣左閉右閉
                dp[l][r] = min(dp[l][r], dp[l][k] + dp[k][r] + cost[l] * cost[k] * cost[r]);
            }
        }
    }
    cout<<dp[0][n-1]<<'\n';
}
```



<br/>

### 學習到的新技巧 or 概念 ~

- 初接觸區間 DP ，感覺很酷。
- 區間 DP: **最佳子結構、重複子問題**
- 區間 DP 常見結構 (左閉右閉為例) :
$DP[L][R] = min^{R-1}_{i = L} \lbrace DP[L][i] + DP[i+1][R] + w(L, i, R) \rbrace$

- 枚舉 **區間長度**
