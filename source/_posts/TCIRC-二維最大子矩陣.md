---
title: TCIRC | 二維最大子矩陣
date: 2021-05-01 12:18:31
tags:
- DP
- AP325
- Classic
- 1-STAR
math: true
---

***Difficulty:***
:star:

## Problem Description 題敘:
[Link](https://judge.tcirc.tw/ShowProblem?problemid=d073)

輸入一個 m*n 的二維整數矩陣，要找一塊總和最大的連續子矩陣，輸

出其總和。以下圖為例，挑選 A[1 , 3] [2 , 3] 可以獲得最大總和 13。	

2 -2 3 3

-6 5 2 -8

3 7 -2 4

---

<!-- more -->


## Thinking 思考歷程:

經典題。這題因為之前有看過類似的題目，加上看到測資不大，所以能想出 $O(m^2n)$ 做法。比較特別的一點是，我們為了處裡二維壓成一維的 "最大連續和"，總不可能再一個一個慢慢加吧，複雜度會爛掉。所以要能快速求得區間和 (不用修改值的)， 所以必須做點類似前綴和的處理 ? 做前綴和時也能想到，如果陣列從 $1$ 開始會比較好實作。 (不會有索引是負的情形)

## Solution 題解:   

因為這題的解題關鍵是把二維壓成一維的最大連續和，因此若我們要枚舉 $m$ ，那就要對每個 $n$ 做前綴和的處理 (在底下的 code 中，用 `rowpsum[][]` 這個變數)。其實還滿直覺的，用兩層 `for` 枚舉 所有 $m$ 的範圍，如此我們就能確定答案的長 (寬) 在第 $i$ 行與第 $j$ 之間，接著就是處理另一邊的長度了。那這個時候如果把我們預先處理的前綴和拿來用 ，就可以把二維最大矩陣和當作一維最大連續和了。舉個例: 如果我們想知道第 $k$ 列的第 3 行到第 5 行的和，就直接用 $S_{k_5}-S_{k_{3-1}}$ 。這也就是為何陣列要從 $1$ 開始，不然的話可能會變成 $S_{k_{0-1}}$ 喔。

## AC Code 程式碼:

```cpp
#include <bits/stdc++.h>
typedef long long LL;
#define IOS ios_base::sync_with_stdio(false), cin.tie(0), cout.tie(0);
using namespace std;
const int N = 205;
int rowpsum[N][N] = {};
int arr[N][N];
int main(){
	//start from 1
	//Complexity: O(m^2*n)
	IOS;
	int m,n;
	cin>>m>>n;
	for(int i = 1; i<=m; i++){
		for(int j = 1; j <=n; j++){
			cin>>arr[i][j];
		}
	}
	//make prefix sum
	int temp = 0;
	for(int i = 1; i<=n; i++){
		temp = 0;
		for(int j = 1; j<=m; j++){
			temp += arr[j][i];
			rowpsum[i][j] = temp;
		}
	}

	int mx = 0;
	//the first two for loops will be enumerating the rows.
	for(int i = 1; i <= m; i++){ //枚舉開頭
		for(int j = i; j<= m; j++){
			int psum = 0;
			for(int k = 1; k <= n; k++){ 
				int val = rowpsum[k][j] - rowpsum[k][i-1];
				if(psum + val < 0) psum = 0;
				else psum += val;
				mx = max(mx, psum);
				
			}
		}
	}
	cout<<mx<<'\n';
}
```

<br/>

### 學習到的新技巧 or 觀念 ~

- 前綴和很好用!




