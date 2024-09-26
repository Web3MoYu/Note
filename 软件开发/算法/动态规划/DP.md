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

## 预测赢家

给你一个整数数组 `nums` 。玩家 1 和玩家 2 基于这个数组设计了一个游戏。

玩家 1 和玩家 2 轮流进行自己的回合，玩家 1 先手。开始时，两个玩家的初始分值都是 `0` 。每一回合，玩家从数组的任意一端取一个数字（即，`nums[0]` 或 `nums[nums.length - 1]`），取到的数字将会从数组中移除（数组长度减 `1` ）。玩家选中的数字将会加到他的得分上。当数组中没有剩余数字可取时，游戏结束。

如果玩家 1 能成为赢家，返回 `true` 。如果两个玩家得分相等，同样认为玩家 1 是游戏的赢家，也返回 `true` 。你可以假设每个玩家的玩法都会使他的分数最大化。

**示例 1：**

```
输入：nums = [1,5,2]
输出：false
解释：一开始，玩家 1 可以从 1 和 2 中进行选择。
如果他选择 2（或者 1 ），那么玩家 2 可以从 1（或者 2 ）和 5 中进行选择。如果玩家 2 选择了 5 ，那么玩家 1 则只剩下 1（或者 2 ）可选。 
所以，玩家 1 的最终分数为 1 + 2 = 3，而玩家 2 为 5 。
因此，玩家 1 永远不会成为赢家，返回 false 。
```

**示例 2：**

```
输入：nums = [1,5,233,7]
输出：true
解释：玩家 1 一开始选择 1 。然后玩家 2 必须从 5 和 7 中进行选择。无论玩家 2 选择了哪个，玩家 1 都可以选择 233 。
最终，玩家 1（234 分）比玩家 2（12 分）获得更多的分数，所以返回 true，表示玩家 1 可以成为赢家。
```

**暴力递归**

```java
public boolean predictTheWinner(int[] nums) {
    int sum = 0;
    for (int cur : nums){
        sum += cur;
    }
    int num = pre(nums, 0, nums.length - 1);
    System.out.println(num);
    if (sum - num > num){
        return false;
    }
    return true;
}

public int pre(int[] nums, int i, int j){
    if (i == j){
        return nums[i];
    }

    return Math.max(nums[i] + pos(nums, i + 1, j), nums[j] + pos(nums, i, j - 1));
}

public int pos(int[] nums, int i, int j){
    if (i == j){
        return 0;
    }
    return Math.min(pre(nums, i + 1, j), pre(nums, i, j - 1));
}
```

**记忆化搜索**

```java
int[][] dp1 = new int[21][21];
int[][] dp2 = new int[21][21];

public boolean predictTheWinner(int[] nums) {
    int sum = 0;
    for (int cur : nums) {
        sum += cur;
    }
    int n = nums.length;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (i == j) {
                dp1[i][j] = nums[i];
            } else {
                dp1[i][j] = -1;
            }
        }
    }

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (i == j) {
                dp2[i][j] = 0;
            } else {
                dp2[i][j] = -1;
            }
        }
    }

    int num = pre(nums, 0, nums.length - 1);
    System.out.println(num);
    if (sum - num > num) {
        return false;
    }
    return true;
}

public int pre(int[] nums, int i, int j) {
    if (dp1[i][j] != -1){
        return dp1[i][j];
    }
    if (i == j) {
        return dp1[i][j];
    }
    dp1[i + 1][j] = nums[i] + pos(nums, i + 1, j);
    dp1[i][j - 1] = nums[j] + pos(nums, i, j - 1);
    return Math.max(dp1[i + 1][j], dp1[i][j - 1]);
}

public int pos(int[] nums, int i, int j) {
    if (dp2[i][j] != -1){
        return dp2[i][j];
    }
    if (i == j) {
        return dp2[i][j];
    }
    dp2[i + 1][j] = pre(nums, i + 1, j);
    dp2[i][j - 1] = pre(nums, i, j - 1);
    return Math.min(dp2[i + 1][j], dp2[i][j - 1]);
}
```

**DP**

```java
public boolean predictTheWinner(int[] nums) {
    int sum = 0;
    for (int cur : nums) {
        sum += cur;
    }
    int n = nums.length;
    int[][] dp1 = new int[n][n];
    int[][] dp2 = new int[n][n];
    for (int i = 0; i < n; i++) {
        dp1[i][i] = nums[i];
    }

    int row = 0;
    int col = 1;
    while(col < n){
        int i = row;
        int j = col;
        while(i < n && j < n){
            dp1[i][j] = Math.max(nums[i] + dp2[i + 1][j], nums[j] + dp2[i][j - 1]);
            dp2[i][j] = Math.min(dp1[i + 1][j], dp1[i][j - 1]);
            i++;
            j++;
        }
        col++;
    }
    int num = dp1[0][n - 1];
    if (sum - num > num) {
        return false;
    }
    return true;
}
```

## 骑士在棋盘上的概率

在一个 `n x n` 的国际象棋棋盘上，一个骑士从单元格 `(row, column)` 开始，并尝试进行 `k` 次移动。行和列是 **从 0 开始** 的，所以左上单元格是 `(0,0)` ，右下单元格是 `(n - 1, n - 1)` 。

象棋骑士有8种可能的走法，如下图所示。每次移动在基本方向上是两个单元格，然后在正交方向上是一个单元格。

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/knight.png)

每次骑士要移动时，它都会随机从8种可能的移动中选择一种(即使棋子会离开棋盘)，然后移动到那里。

骑士继续移动，直到它走了 `k` 步或离开了棋盘。

返回 *骑士在棋盘停止移动后仍留在棋盘上的概率* 。

**示例 1：**

```
输入: n = 3, k = 2, row = 0, column = 0
输出: 0.0625
解释: 有两步(到(1,2)，(2,1))可以让骑士留在棋盘上。
在每一个位置上，也有两种移动可以让骑士留在棋盘上。
骑士留在棋盘上的总概率是0.0625。
```

**示例 2：**

```
输入: n = 1, k = 0, row = 0, column = 0
输出: 1.00000
```

**暴力递归**

```java
public double knightProbability(int n, int k, int row, int column) {
    return process(n, k, row, column) / Math.pow(8, k);
}

public double process(int n, int k, int row, int col) {
    if (row < 0 || row >= n || col < 0 || col >= n) {
        return 0;
    }
    if (k == 0) {
        return 1;
    }
    return process(n, k - 1, row + 2, col + 1)
            + process(n, k - 1, row + 2, col - 1)
            + process(n, k - 1, row - 2, col + 1)
            + process(n, k - 1, row - 2, col - 1)
            + process(n, k - 1, row + 1, col + 2)
            + process(n, k - 1, row + 1, col - 2)
            + process(n, k - 1, row - 1, col + 2)
            + process(n, k - 1, row - 1, col - 2);
}
```



**记忆化搜索**

```java
public double knightProbability(int n, int k, int row, int column) {

    double[][][] dp = new double[k + 1][n][n];
    for (int i = 0; i < k + 1; i++) {
        for (int j = 0; j < n; j++) {
            for (int t = 0; t < n; t++) {
                dp[i][j][t] = -1;
                dp[0][j][t] = 1.0;

            }
        }
    }
    process(n, k, row, column, dp);
    return dp[k][row][column] / Math.pow(8, k);
}

public double process(int n, int k, int row, int col, double[][][] dp) {
    if (row < 0 || row >= n || col < 0 || col >= n) {
        return 0;
    }
    if (dp[k][row][col] != -1) {
        return dp[k][row][col];
    }
    dp[k][row][col] = process(n, k - 1, row + 2, col + 1, dp)
            + process(n, k - 1, row + 2, col - 1, dp)
            + process(n, k - 1, row - 2, col + 1, dp)
            + process(n, k - 1, row - 2, col - 1, dp)
            + process(n, k - 1, row + 1, col + 2, dp)
            + process(n, k - 1, row + 1, col - 2, dp)
            + process(n, k - 1, row - 1, col + 2, dp)
            + process(n, k - 1, row - 1, col - 2, dp);
    return dp[k][row][col];
}
```

**DP**

```java
public double knightProbability(int n, int k, int row, int column) {
    int[][] dirs = { { -2, -1 }, { -2, 1 }, { 2, -1 }, { 2, 1 }, { 1, 2 }, { 1, -2 }, { -1, -2 }, { -1, 2 } };
    double[][][] dp = new double[k + 1][n][n];
    for (int m = 0; m < k + 1; m++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (m == 0) {
                    dp[m][i][j] = 1;
                } else {
                    for (int[] dir : dirs) {
                        int ii = i + dir[0];
                        int jj = j + dir[1];
                        if (ii >= 0 && ii < n && jj >= 0 && jj < n) {
                            dp[m][i][j] += dp[m - 1][ii][jj];
                        }
                    }
                }
            }
        }
    }

    return dp[k][row][column] / Math.pow(8, k);
}
```

## 零钱找零

> 给你一个无重复的正整数数组$num$,对于任意一个元素$num[i]$代表有该货币无限张，再给目标钱数$aim$问，有多少种方式能组成$aim$

**暴力递归**

```Java
public int solve(int[] arr, int aim){
    return process(arr, 0, aim);
}

// 可以自由使用arr[index....]所有的面值
// 需要搞定的钱数是rest
// 返回找零的方法数
public int process(int[] arr, int index, int rest){
    if (index == arr.length){
        return rest == 0 ? 1 : 0;
    }
    int ways = 0;
    for (int num = 0; arr[index] * num <= rest; num++){
        ways += process(arr, index + 1, rest - arr[index] * num)
    }
    return ways;
}
```

**DP(不优化)**

```java
public int solve(int[] arr, int aim){
    if (arr == null || arr.length == 0){
        return 0;
    }
    int n = arr.length;
    int[][] dp = new int[n + 1][aim + 1];
    dp[n][0] = 1;
    for (int index = n - 1; index >= 0; index--){
        for (int rest = 0; rest <= aim; rest++){
            int ways = 0;
    		for (int num = 0; arr[index] * num <= rest; num++){
        		ways += dp[index + 1][rest - arr[index] * num];
    		}
    		 dp[index][rest] = ways;
        }
    }
    return dp[0][aim];
}
```

**DP**

```java
public int solve(int[] arr, int aim){
    if (arr == null || arr.length == 0){
        return 0;
    }
    int n = arr.length;
    int[][] dp = new int[n + 1][aim + 1];
    dp[n][0] = 1;
    for (int index = n - 1; index >= 0; index--){
        for (int rest = 0; rest <= aim; rest++){
    		dp[index][rest] = dp[index + 1][rest];
            if (rest - arr[index] >= 0){
                dp[index][rest] += dp[index][rest - arr[index]];
            }
        }
    }
    return dp[0][aim];
}
```

## 最小路径和

给定一个包含非负整数的 `*m* x *n*` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**说明：**每次只能向下或者向右移动一步。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/11/05/minpath.jpg)

```
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

**示例 2：**

```
输入：grid = [[1,2,3],[4,5,6]]
输出：12
```

**暴力递归**

```java
public int minPathSum(int[][] grid) {
    return process(grid, 0, 0);
}

public int process(int[][] grid, int i, int j) {
    if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length) {
        return Integer.MAX_VALUE;
    }
    if (i == grid.length - 1 && j == grid[0].length - 1) {
        return grid[i][j];
    }
    return Math.min(process(grid, i + 1, j), process(grid, i, j + 1)) + grid[i][j];
}
```

**记忆化搜索**

```java
public int minPathSum(int[][] grid) {
    int m = grid.length;
    int n = grid[0].length;
    int[][] dp = new int[m][n];
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            dp[i][j] = -1;
        }
    }
    dp[m - 1][n - 1] = grid[m - 1][n - 1];
    return process(grid, 0, 0, dp);
}

public int process(int[][] grid, int i, int j, int[][] dp) {
    if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length) {
        return Integer.MAX_VALUE;
    }
    if (dp[i][j] != -1) {
        return dp[i][j];
    }
    dp[i][j] = Math.min(process(grid, i + 1, j, dp), process(grid, i, j + 1, dp)) + grid[i][j];
    return dp[i][j];
}
```

