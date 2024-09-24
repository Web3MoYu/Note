# 动态规划

## 机器人走路

> 给定一个数$N$代表有$1-N$个数，然后给定起点$cur$，终点$e$，剩余$rest$步，必须走完$rest$到达$e$有多少种方式

**暴力递归**

```java
public int process(int n, int e, int rest, int cur){
    if (rest == 0){ // dp的初始值
        return cur == e ? 1 : 0;
    }
    if (cur == 1){ // cur=1代表当前依赖右上角的
        return process(n, e, rest - 1, 2);
    }
    if(cut == n){ // 依赖右上角
        return process(n, e, rest - 1, n - 1);
    }// 依赖左上角+右上角
    return process(n, e, rest - 1, cur - 1) + process(n, e, rest - 1, cur + 1);
}
```

**DP**

```java
public int dp(int n, int e, int k){
    int[][] dp = new int[k + 1][n + 1];
    dp[0][e] = 1;
    for (int i = 1; i <= k; i++){
        for (int j = 1; j <= n; j++){
            if (j == 1){
                dp[i][j] = dp[i - 1][2];
            }else if (j == n){
                dp[i][j] = dp[i - 1][j - 1];
            }else{
                dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j + 1]
            }
        }
    }
    return dp[k][e];
}
```



**记忆化搜索**

```java
public int walkWay(int N, int E, int S, int K){
    // 二维表，缓存
    int[][] dp = new int[K + 1][N + 1];
    for (int i = 0; i <= K; i++){
        for (int j = 0; j <= N; j++){
            dp[i][j] = -1;
        }
    }
    return process(N, E, K, S, dp)
}

public int process(int n, int e, int rest, int cur, int[][] dp){
    // 如果已经有了该数据，直接返回
    if (dp[rest][cur] != -1){
        return dp[rest][cur];
    }
    
    // 缓存未命中
    if (rest == 0){
        dp[rest][cur] = cur == e ? 1 : 0;
        return cur == e ? 1 : 0;
    }
    if (cur == 1){
      	dp[rest][cur] = process(n, e, rest - 1, 2);
    }
    else if(cut == n){
        dp[rest][cur] = process(n, e, rest - 1, n - 1);
    }else{
    	dp[rest][cur] = process(n, e, rest - 1, cur - 1) 
            + process(n, e, rest - 1, cur + 1);
    }
    return dp[rest][cur];
}
```

## 最少硬币数量

> 给你一个数组，数组中的每个元素代表一个硬币如$[2,7,3,5,3]$，在给定一个数$num$，请给出组成这个数的最少硬币数量，本例：$7+3=10$

**暴力递归**

```java
public int solve(int[] arr, int aim){
    return process(arr, 0, aim);
}

// arr[index....]组成aim这么多钱，最少的硬币数量返回
public int process(int[] arr, int index, int aim){
    if (aim < 0){
        return -1;
    }
    if (aim == 0){
        return 0;
    }
    // aim > 0
    if (index == arr.length){
        return -1;
    }
    // aim > 0 并且 有硬币
    int p1 = process(arr, index + 1, aim); // 不要当前数
    int p2 = process(arr, index + 1, aim - arr[index]); // 要当前数
    if (p1 == -1 && p2 == -1){
        return -1;
    }else{
        if (p1 == -1){
            return p2 + 1;
        }
        if (p2 == -1){
            return p1;
        }
        return Math.min(p1, p2 + 1);
    }
}
```

**记忆化搜索**

```java
public int solve(int[] arr, int aim){
    int[][] dp = new int[arr.length + 1][aim + 1];
    for (int i = 0; i <= arr.length; i++){
        for (int j = 0; j <= arr.length; j++){
        	dp[i][j] = -2;
    	}
    }
    return process(arr, 0, aim, dp);
}

// arr[index....]组成aim这么多钱，最少的硬币数量返回
public int process(int[] arr, int index, int aim, int[][] dp){
    if (aim < 0){
        return -1;
    }
    if (dp[index][aim] != -2){
        return dp[index][aim];
    }
    if (aim == 0){
        dp[index][aim] = 0;
    }else if (index == arr.length){
        dp[index][aim] = -1;
    }else{
        // aim > 0 并且 有硬币
    	int p1 = process(arr, index + 1, aim); // 不要当前数
    	int p2 = process(arr, index + 1, aim - arr[index]); // 要当前数
        if (p1 == -1 && p2 == -1){
        	dp[index][aim] = -1;
    	}else{
        	if (p1 == -1){
            	dp[index][aim] = p2 + 1;
        	}
        	if (p2 == -1){
            	dp[index][aim] = p1;
        	}
        	dp[index][aim] = Math.min(p1, p2 + 1);
    	}
    }
    return dp[index][aim]; 
}
```

**DP**

```java
public int solve(int[] arr, int aim){
    int N = arr.length;
    int[][] dp = new int[arr.length + 1][aim + 1];
    for (int row = 0; row <= N; row++){
        dp[row][0] = 0;
    }
    for (int col = 1; col <= aim; col++){
        dp[N][col] = -1;
    }
    for (int index = N - 1; index >= 0; index--){
        for (int rest = 1; rest <= aim; rest++){
            int p1 = dp[index + 1][rest];
            int p2 = -1;
            if(rest - arr[index] >= 0){
                p2 = dp[index + 1][rest - arr[index]];
            }
            if (p1 == -1 && p2 == -1){
                dp[index][rest] = -1;
            }else{
                if (p1 == -1){
                    dp[index][rest] = p2 + 1;
                }
                if (p2 == -1){
                    dp[index][rest] = p1;
                }
                dp[index][rest] = Math.min(p1, p2 + 1);
            }
        }
    }
    return dp[0][aim];
}
```

