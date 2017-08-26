## Coding

**IO優化**

````C++
ios_base::sync_with_stdio(false);
cin.tie(0);
````

## Graph Theory



## Dynamic Programming

- **Fibonacci Number**

- **最大連續子序列和**

  ```c++
  // dp[i] 帶表 a[i] 結尾的 max
  max( dp[i-1] + a[i] , a[i] );
  // 最後找 dp[1]~dp[n] 的 max
  ```

- **最大子序列和**    $O(N^2)$

  - 紀錄以a[i]結尾的max

- **LIS** (最長嚴格遞增子序列)    $O(NlogN)$

```C++
  vector<int> v;
  v.push_back(a[0]); // important
  for(int i=1; i<n; ++i){
  	if( a[i] > v.back() )
  		v.push_back( a[i] );
  	else
  		*lower_bound( v.begin(), v.end(), a[i] ) = a[i];
  }
  cout << v.size() << "\n";
```

- 也可以排序後 做 LCS

- 如果是非遞減的子序列 取代v的時候 要是vector有那個數字了 改用`upper_bound()`

- **LCS** (最長共同子序列)

- 最長迴文子序列

  - 反過來兩個LCS就好了

- **背包問題**

  - 三種 懶的打了
  - 正整數分兩堆差最小 $|S_1-S_2|$
    - 背包是全部合一半的01背包問題

- **區間合併**

  - 矩陣連乘    $O(N^3)$

  - 1~n堆(一直線) 每次合併2個 剩一個為止

    - 隨便兩個 -> Greedy(霍夫曼編碼)    $O(NlogN)$

    - 相鄰兩個 -> `dp[l][r]`    $O(N^3)$

      ```C++
      // 先算前綴和
      // 據說可以平方時間 以後再說
      ```

  - 1~n堆(環) 每次合併相鄰2個 剩一個為止

    - 改成(1~n)+(1~n) 2n個物品 變成用上面的方法 最後找長度為N的某段

- **換硬幣** 

  ```C++
  DP(n,m) = min( DP(n-1,m) , DP(n,m-price[n]) + 1 );
  // n是$$種類 m是金額
  ```

- **Edit distance** 編輯距離

- **整數分拆**

- **Activity Selection** 工作排程

  - 無權重 -> Greedy (Earliest finish time)    $O(N)$

  - 有權重 -> DP    $O(NlogN)$ binary_search 找不衝突事件

    $ dp[i] = \left\{ \begin{array}{l} 0 , i=0\\ max(dp[j] + W_i ,  dp[i-1])\end{array} \right.$

    $A_j$ 是不和 $A_i$ 衝突的最接近事件 $j<i$

- **TSP**    $O(2^N)$ 位元DP

- ​