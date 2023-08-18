---
title: TIOJ-1063  | 最大矩形(Area) & TCGS-b027 | 小綠人的城堡
date: 2021-05-02 10:31:17
tags:
- DP
- Classic
- TIOJ
- TCGS
- 2-STAR
- 3-STAR
math: true
---

***Difficulty:***

TCGS-b027: :star::star:

T​IO​J-1603: :star::star::star:

## Problem Description 題敘:

[TCGS-b027 Link](http://www.tcgs.tc.edu.tw:1218/ShowProblem?problemid=b027)
[TIOJ-1063 Link](https://tioj.ck.tp.edu.tw/problems/1063)

兩題類似題，放在一起。

TCGS-b027: 給一個二維陣列，求全部由 $0$ 組合成的正方形區域的最大面積有多少?

TIOJ-1063: 給一個二維陣列，求全部由 $1$ 組成的最大矩形面積有多少?

---

<!-- more -->



## Thinking 思考歷程: 

**TCGS-b027:** 
如果暴力硬做的話鐵定不行，那怎麼辦? 如果掃到 $1$ 這個數字是鐵定，不可能由他構成正方形的。既然知道是 $dp$ ，那就必須想辦法利用之前算過的答案來得到這個解。那如果我依序把矩陣掃過去的話，那麼在已經算過的解當中，在我們的左方或上方的解應該會跟當前這項的解有比較大的關系 ($\because$ 是正方形嘛)。 朝這個方向想答案就出來了 ~

**TIOJ-1063:** 
這題把 $0$ 換成 $1$ 。然後這題似乎再比剛剛那題難一點點，因為這次的形狀只要符合矩形的定義就好，如果只用正方形的算法，肯定會少算。那我嘗試作些改變，若對於一個當前項，把往左邊、與往上看的值記成一個 $pair$ 而非取 $min$ 呢? 好像也沒有太大的作用耶。怎麼辦? 想好久還是想不到... :cry: 只好去看題解。

## Solution 題解:

**TCGS-b027:**
由以上的思路，如果我這項是 $0$ ，才有可能會創造出更大的正方形。我往左 & 往上看之前算過的解，先看看左邊鄰居，若以左邊鄰居作為一個正方形右下角的邊長，最大會是多少；再來看看樓上的鄰居，若樓上的鄰居作為另一個正方形右下角的邊長，最大會是多少。兩個值取 $min$ 再 $+ 1$ 就是以當前的這項為右下角的新最大正方形邊長。之後每算完一次就和目前的最大值 $max$，最後的 $max$ 再平方就結束了。

**TIOJ-1063:**
要構成一個矩形的條件，首先就是要確定長或寬其中一個邊。往這個方向想，我們用兩個 `for` 照順序把矩陣掃過去，當掃到第 `j` 行，用一個 `len[j]` 來記錄由 `j` 往上看，最多有幾個一。(這步 $dp$ 我真的想不到) 如果我當前的元素是 $0$ 的話就把 `len[j]` 這列歸零，否則就是 *上一行掃的這列的數量 $+1$* 。這**暫且**確定了**直向**的長度。那在這裡先偷偷把橫向的長度當作一，取一次 $max$。之後把 `mn` 先設成 `len[j]` 當作 *當前的直向最小值*，在此也初始一個變數 `k` 為 `j-1`，這兩個等等會用到。
\
現在來處理**橫向**的。由我們的 `j` 由後往前掃來過展我們橫向邊，一直拓展到最左邊或遇到 $0$ 。但是，如果要拓展橫向到 `k` 的話，那其實直向長度 `len[k]` 有可能更長或更短，那為了符合需求，只能將就取最短的。這個時候我們的 `mn` 就派上用場了。`mn = min(mn, len[k])` 紀錄目前的直向最小值，有了這個以後，我們就能安心的往作擴展，並把 `k-1 * (j-k)` 在和 `mx` $max$ 一下就是目前的最大矩形面積。(之所以要把 `k` 減掉 $1$ 是因為我們既然已經 $min$ 過 `len[k]` ，所以橫向的長度必須要包括他。如果這個不好理解的話，可以想想開始初始條件: 我們以經偷偷算過橫向長度為 $1$ 的解了，而 `k` 又從 `j-1` 開始遞減，如果直接拿 `k-(j-1) = 1` 肯定錯) 

##  AC Code 程式碼:    

TCGS-b027:

```cpp
#include <iostream>
typedef long long LL;
#define IOS ios_base::sync_with_stdio(false), cin.tie(0), cout.tie(0);
using namespace std;
const int N = 1e2;
int dp[N][N] = {}, arr[N][N];
int main(){
	IOS; int h,w; cin>>h>>w;
	for(int i = 0; i<h; i++){
		for(int j = 0; j <w; j++){
			 cin>>arr[i][j];
		}
    }
	int mx = 0;
	for(int i = 1; i<h; i++){
		for(int j = 1; j<w; j++){
			if(arr[i][j] == 1) continue;
			dp[i][j] = min(dp[i-1][j] + 1, dp[i][j-1]+1);
			mx = max(mx, dp[i][j]);
		}
	}

	cout<<mx*mx<<'\n';
}
```



TIOJ-1063: ([參考資料](https://yuihuang.com/tioj-1063/))

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 205;
int arr[N][N] = {}, len[N] = {};
int main(){ 
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);
    int m,n; cin>>m>>n;
    for(int i = 0; i<m; i++) for(int j = 0; j<n; j++) cin>>arr[i][j];
    int mx = 0;
    for(int i = 0; i<m; i++){
        for(int j = 0; j<n; j++){
            len[j] = (arr[i][j] ? len[j] + 1 : 0); //最長的直向
            mx = max(mx, len[j] * 1);
            int k = j - 1;
            int mn = len[j];
            if(len[j] == 0) continue;
            while(k>=0 and arr[i][j]){
                mn = min(mn, len[k]); //新橫向
                k--;
                mx = max(mx, mn*(j-k));
            }
        }
    }
    cout<<mx<<'\n';
}
```

NOTE: 第 16 行可以直接 `continue` 其實是因為如果本身是 $0$ 的話其實沒甚麼好拓展的，答案都是 $0$ ，因此在某些條件下能省一些時間。

<br/>

### 學習到的新技巧 or 概念 ~

- TIOJ-1063: 連續元素的 $dp$ 