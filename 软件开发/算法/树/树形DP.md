# 递归套路(树形DP)

对于二叉树的题目，如果你可以从左右子树上拿信息，然后满足某些条件即可使用。

## 套路



- 1. 以某个节点X为头节点的子树中，分析答案有哪些可能性，并且这种分析是以X的左子树、X的右子树和X整棵树的角度来考虑可能性的
- 2. 根据第一步的可能性分析，列出所有需要的信息
- 3. 合并第二步的信息，对左树和右树提出同样的要求，并写出信息结构
- 4. 设计递归函数，递归函数是处理以X为头节点的情况下的答案。包括设计递归的base case,默认直接得到左树和右树的所有信息，以及把可能性做整合，并且要返回第三步的信息结构这四个小步骤

比如判断平衡二叉树

可能性：

- 左子树要是平衡二叉树
- 右子树要是平衡二叉树
- 左右字数高度差小于1

需要的信息：

- 左子树是否平衡
- 左子树高度
- 右子树是否平衡
- 右子树高度

查看上述代码即可

**判断是否是二叉搜索树**

> 对于每一个子树来说我都需要以下信息，用于判断是否满足二叉搜索树

```java
ReturnData{
    boolean isBST;
    int min;
    int max;
}
```

```java
public static ReturnData process(TreeNode root){
    if (root == null){ // 因为min和max不好复制所以返回null
        return null;
    }
    
    // 拿到左右子树的信息
    ReturnData leftData = process(root.left);
    ReturnData rightData = process(root.right);
    
    // 拿到信息后，需要返回构造递归的闭环
    boolean isBST = true;
    int min = root.value;
    int max = root.value;
    
    // 处理min,max
    if (leftData != null){
        min = Math.min(min, leftData.min);
        max = Math.max(max, leftData.max);
    }
    if (rightData != null){
        min = Math.min(min, rightData.min);
        max = Math.max(max, rightData.max);
    }
    
    // 处理isBST
    if (leftData != null && (!leftData.isBST || leftData.max >= root.val)){
        isBST = false;
    }
    if (RightData != null && (!rightData.isBST || root.val >= rightData.min)){
        isBST = false;
    }
    
    return new ReturnData(isBST, min, max);
}
```

## 题目

### **最低公共祖先**

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    // 如果子树上没有p或q就肯定会返回null，有的话就返回
    if (root == null || root == p || root == q) {
        return root;
    }

    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);

    // 自己左右孩子都不为空就返回自己
    if (left != null && right != null) {
        return root;
    }
    // 谁为空返回另外一个，遍历另外一个子树
    return left == null ? right : left;
}
```

### **得到后继节点**

对于后继节点有以下两种情况

1. 如果该节点有右树，那么后继节点就是右树最左的孩子
2. 如果没有右树，那么后继节点就是一个左孩子的父节点，就是一直往父节点回溯，且某个父节点是左孩子（注意最右的孩子是没有的(root)）

```java
public static TreeNode getSuccessorNode(TreeNode root){
    if (root == null){
        return root;
    }
    if (root.right != null){
        return getLeftMost(root.right);
    }else{
        TreeNode parent = root.parent;
        // 我一直是我父亲的右孩子
        while(parent != null && parent.left != root){
            root = parent;
            parent = root.parent;
        }
        return parent;
    }
}

public static TreeNode getLeftMost(TreeNode root){
    if (root == null){
        return root;
    }
    while(root.left != null){
        root = root.left;
    }
    return root;
}
```

### **折叠纸条**

> 将一张纸条对折N次，打印凹凸情况。
>
> 就是一棵树如果对折三次为
>
> ​																凹
>
> ​													凹						凸
>
> ​											凹		 	凸		  凹 				凸
>
> 然后中序遍历就是从上到下的凹凸情况，除了根节点是凹之外，对于每一个左子树的根都是凹，每一个的右子树的根都是凸

```java
// i是节点的层数		N层数		down==true 凹 down == false 凸
solve(int i, int N. boolean down){
    if (i > N){
        return;
    }
    solve(i + 1, N, true);
    print(down ? "凹 " : "凸");
    solve(i + 1, N， false);
}

solve(1,N,true);
```

### 二叉树节点间的最大距离

> 从二叉树的节点出发，可以向上或者向下走，但沿途的节点只能经过一次，到达节点b时路径上的节点个数叫作到b的距离，那么二叉树任何两个节点之间都有距离，求整棵树上的最大距离。

本题的解法是按照头节点参与不参与分成两种情况

- 参与
  - 左子树最大距离和右子树最大距离取最大值
- 不参与
  - 左子树的高+1+右子树的高

最后二者取最大值

```java
class Info{
    public int maxDistance;
    public int height;
    public Info(int dis, int h){
        this.maxDistance = dis;
        this.height = h;
    }
}

// 返回以root为头的整个树的两个信息
public Info process(TreeNode root){
    if (root == null){
        return new Info(0, 0);
    }
    Info leftInfo = process(root.left);
    Info rightInfo = process(root.right);
    
    // info
    int p1 = leftInfo.maxDistance;
    int p2 = rightInfo.maxDistance;
    int p3 = leftInfo.height + 1 + rightInfo.height;
    int maxDistance = Math.max(p3, Math.max(p1, p2));
    int height = Math.max(leftInfo.height, rightInfo.height) + 1;
    return new Info(maxDistance, height);
}
```

