# 总结

先看后序遍历的模板，可以看出它可以先可以先搜集左右子树的信息，然后整合成自己的信息，再传递下去。

这样，一个大的问题，可以分成两个小的问题，且大的问题可以由两个小的问题的接构成，这是典形的DP问题啊。

```java
void post(TreeNode node){
    // 推出条件
    if(node==null) return ;
    post(node.left);
    post(node.right);
    // 后序遍历的处理
}

```

上框架:

```java
Info post(TreeNode node){
    // 退出条件
    if(node==null) return ;
    
    Info left = post(node.left);
    Info right = post(node.right);
    // 后序遍历的处理
    // 根据左子树的info和右子树的info构建出自己的info。
    return cur;
}

// Info封装的是每一层需要记录的信息
class Info{
    // 定义每一层需要记录的信息
}

```

二叉树的递归套路：

1. 假设以X节点为头，假设可以向X左子树和X右子树要任何信息；
2. 在上一步的假设上，讨论以x为头节点的树，得到答案的可能性(最重要)， 一般以是否使用X为划分；
3. 列出所有可能性后，确定到底需要向左树和右树要什么样的信息
4. 把左树信息和右树信息求全集，就是任何一颗子树都需要返回的信息S
5. 递归函数都返回S, 每一棵子树都这么要求
6. 写代码，在代码中考虑如何把左子树的信息和右子树的信息整合出整颗树的信息



# 常见题库

1. 判断平衡树
2. 二叉树最大距离
3. 二叉树中最大二叉搜索子树的长度
4. 二叉树中最大二叉搜索子树的头节点
5. 派对的最大快乐值
6. 判断满二叉树
7. 判断一棵树是不是完全二叉树
8. 二叉树的公共祖先



# 1. 判定平衡二叉树

分析：判定当前x为平衡二叉树，需要左右子树都是平衡二叉树，且高度差小于2；

因此，每个节点需要给出信息isbls和height， 

x的这两个信息都可以由左右子树推导出来！

```java

public boolean isBalanced(TreeNode node){
    return help(node).isbls;
}

Info help(TreeNode node){
    if (node==null) {
        return new Info(true, 0);
    }
    Info l = help(node.left);
    Info r = help(node.right);
    Boolean isbls = false;
    if (l.isbls && r.isbls && Math.abs(l.height-r.height)<2){
        isbls = true;
    }
    int height = Math.max(l.height,r.height)+1;
    return new Info(isbls, height);
}

class Info{
    boolean isbls;
    int height;
    Info(boolean isbls, int h){
        this.isbls = isbls;
        height = h;
    }
}

```





# 整棵二叉树的最大距离

[543. 二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

给定一颗二叉树的头节点，任何两个节点之间存在距离，求最大距离

分析：

对于一个节点x，

如果不经过x， 最大距离可能在左边或者右边

经过x，左树高度+右树高度+1；

所以，需要记录每个节点的

高度和最大距离；

```java

public int diameterOfBinaryTree(TreeNode root) {
	return maxLen(root).maxDis;
}
Info maxLen(TreeNode root){
    if (root==null) {
        new Info(0,0);
    }
    Info l = maxLen(root.left);
    Info r = maxLen(root.right);
    int height = Math.max(l.height, r.height)+1;
    int maxDis = Math.max(l.height+r.height+1,Math.max(l.maxDis, r.maxDis));
    return new Info(height, maxDis);
}

class Info{
    int height;
    int maxDis;
    Info(int h, int d){
        height = h;
        maxDis = d;
    }
}

```



# 3. 二叉树中二叉搜索树的最大长度

分析：

含有x， 左右子树`isAllSearchTree`, 且x>left.max, x<right.min;

不含有x， max(左子树的max, 右子树的maxTree)

需要记录：

boolean `isAllSearchTree` = l.is && r.is && x>l.max && r<r.min;

max  = max(l.max, r.max)

min = min(l.min, r.min)

int `maxSubTree ` = max(l.maxSubTree, r.maxSubTree); 再根据第一个判断

退出条件(初始条件)：

只有一个点的时候，is=true， max=min= x, maxsubTree=1;

```java

int maxBinarySearchTree(TreeNode node){
    if (node==null) return 0;
	return help(node).maxSubTree;	    
}

Info help(TreeNode node){
    // 退出条件 
    // 两种方案：1. if (node==null) return null; 上层节点需要考虑null的情况
    // 方案2： if (node.left==null && node.right== null) return 
    // 		   需要单独考虑只要单侧节点的情况！
    
    
    Info l = help(node.left);
    Info r = help(node.right);
    
    //根据l和r构建当前节点的Info
    boolean isAllSearchTree;
    int max;
    int min;
    int maxSubTree;
    
    return new Info(isAllSearchTree, max, min,maxSubTree);
}


class Info{
	boolean isAllSearchTree;
    int max;
    int min;
    int maxSubTree;
    Info(){
        ...
    }
    
}

```

# 4. 二叉树中最大二叉搜索子树的头节点

3的基础上Info增加一个Node参数；

`root = left.root || right.root || x本身`





# 5. 派对的最大快乐值

公司的员工符合多叉树，每个员工有n个下属，最多只有1个上属，只有一个老板是根节点，基层员工没有下属，即：叶子节点。每个员工都有一个value快乐值

公司举办party，选一些员工来；

1. 如果某个员工来了，那么所有直接下属不能来，
2. party的整体快乐是所有来的员工的value总和；
3. 求party的最大快乐值；



分析

选取x: 不选左子树的最大快乐值+不选右子树的最大快乐值

不选x: max(选左子树的最大值，不选左子树的最大值)+max(选右子树的最大值，不选右子树的最大值)

所以，Info需要记录：

int yes: 选取该节点的快乐值 

int no: 不选该节点的快乐值



退出条件：

if (node==null) return Info(0,0);



# 6. 判断一颗树是不是满二叉树

```java
boolean help(TreeNode node){
    if (node==null) return true;
    if (node.left ==null && node.right==null){
        return true;
    }
    if (node.left ==null || node.right==null){
        return false;
    }
    if (help(node.left) && help(node.right)){
        return true;
    }
    return false;
}

// 方法2
// 满二叉树: 高度h, 节点个数n = 2^h-1;
```



# 7. 判断一棵树是不是完全二叉树



```java

// 方法1：
// 完全二叉树
// 根据 可以得到当前点是否满以及高度。 
// 左子树是否满，以及高度， 是否完全二叉树
// 右子树是否满，以及高度  是否完全二叉树
// 1. 左右都没有缺口，


// BFS
// 1. 一个节点，如果有右子但是没有左孩子，return false,否则继续
// 2. 如果一个节点的孩子不双全， 则后面的节点必须全是叶子节点，否则return false
class Solution {
    public boolean isCompleteTree(TreeNode node) {
        if (node==null) return true;
        Deque<TreeNode> q = new ArrayDeque();
        q.addLast(node);
        while(!q.isEmpty()){
            TreeNode cur = q.removeFirst();
            if (cur.right!=null && cur.left==null){ //有右无左
                return false;
            }
            if(cur.right==null || cur.left==null){ // 只要有一个孩子为null。
                if (cur.left!=null){
                    q.addLast(cur.left);
                }
                while(!q.isEmpty()){
                    TreeNode next = q.removeFirst();
                    if (next.left !=null || next.right!=null){
                        
                        return false;
                    }
                }
                return true;
            }else{ // 双全
                q.addLast(cur.left);
                q.addLast(cur.right);
            }
        }
        return true;
    }
}
```





# 8. 二叉树中 节点a和b的公共祖先

```java

     1
  2      3
4    5  6   7
         
// 方法1：
// 1. 先找到每个节点的path
// 2.求开头的相等元素
boolean parents(TreeNode root, TreeNode a,List<TreeNode> path){
	if(root==null) return false;
    if(root.left!=null){
        path.add(path.left);
        if (parents(root.left, a, path)){
            return true;
        }
        path.remove(path.left);
    }
	if(root.right!=null){
        path.add(path.right);
        if (parents(root.right, a, path)){
            return true;
        }
        path.remove(path.right);
    }  
}

// 方法3: 维护一个hashmap 记录每一个点的父节点



// 2.  post后序dp
/**
如果左子树含有一个节点，右子树含有一个节点，则当前节点是最小的父节点
如果左子树含有两个，则最小父节点在左子树中；

需要记录
1. 当前节点是否含有a
2. 当前节点是否含有b
3. 是否为最小父节点；


退出条件：节点为null。

**/
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        return inOrder(root,  p, q).min;
    }


    Info inOrder(TreeNode root, TreeNode p, TreeNode q){
        if (root==null){
            return new Info(false, false, null);
        }
        Info l = inOrder(root.left, p, q);
        Info r = inOrder(root.right,p, q);
        boolean hasP =( root==p || l.hasP || r.hasP);
        boolean hasQ =( root==q || l.hasQ || r.hasQ);
        TreeNode min=null;
        if (l.min!=null){
            min = l.min;
        }else if (r.min!=null){
            min = r.min;
        }else if(hasP && hasQ){
            min = root;
        }
        return new Info(hasP, hasQ, min);
    }


    class Info {
        boolean hasP;
        boolean hasQ;
        TreeNode min;//可能为null
        Info(boolean p, boolean q, TreeNode min){
            hasP = p;
            hasQ = q;
            this.min =min;
        }
    }
}
```



