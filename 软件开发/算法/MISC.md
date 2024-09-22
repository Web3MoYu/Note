# N皇后

按照国际象棋的规则，皇后可以攻击与之处在同一行或同一列或同一斜线上的棋子。

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回所有不同的 **n 皇后问题** 的解决方案。

每一种解法包含一个不同的 **n 皇后问题** 的棋子放置方案，该方案中 `'Q'` 和 `'.'` 分别代表了皇后和空位。

```java
public static int num(int n){
    if (n < 1){
        return 0;
    }
    int[] record = new int[n];
    return process(0, record, n);
}


// i代表哪一行
public static int process(int i, int[] record, int n){
    if (i == n){
        return 1; // 潜台词：来到i行默认0->i-1都是正确的摆法
    }
    int res = 0;
    for (int j = 0; j < n; j++){
        // 当前i行，放到j列会不会出现共列或共斜线
        if (isValid(record, i, j)){
            record[i] = j;
            // 会累积计算后面的共有多少种摆法
            res += process(i + 1, record, n);
        }
    }
    return res;
}
// 只用检查是不是跟之前的皇后共列和共斜线
public static boolean isValid(int[] record, int i, int j){
    for (int k = 0; k < i; k++){
        // j只会有n-1个值，所以比较当前列之前出现过每
        // record[k] - j 是列的差值
        // i - k 是行的差值
        // 二者的绝对值相等代表共线
        if (j == record[k] || Math.abs(record[k] - j) == Math.abs(i - k)){
            return false;
        }
    }
    return true; 
}
```

