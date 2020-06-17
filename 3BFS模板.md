# 总结

BFS 的核心思想应该不难理解的，就是把一些问题抽象成图，从一个点开始，向四周开始扩散。一般来说，我们写 BFS 算法都是用「队列」这种数据结构，每次将一个节点周围的所有节点加入队列。

BFS 相对 DFS 的最主要的区别是：**BFS 找到的路径一定是最短的，但代价就是空间复杂度比 DFS 大很多**，至于为什么，我们后面介绍了框架就很容易看出来了。

本文就由浅入深写两道 BFS 的典型题目，分别是「二叉树的最小高度」和「打开密码锁的最少步数」，手把手教你怎么写 BFS 算法。

## 一，算法框架

要说框架的话，我们先举例一下 BFS 出现的常见场景好吧，**问题的本质就是让你在一幅「图」中找到从起点`start`到终点`target`的最近距离，这个例子听起来很枯燥，但是 BFS 算法问题其实都是在干这个事儿。**

把枯燥的本质搞清楚了，再去欣赏各种问题的包装才能胸有成竹嘛。

这个广义的描述可以有各种变体，比如**走迷宫**，有的格子是围墙不能走，从起点到终点的最短距离是多少？如果这个迷宫带「传送门」可以瞬间传送呢？

再比如说**两个单词，要求你通过某些替换**，把其中一个变成另一个，每次只能替换一个字符，最少要替换几次？

再比如说**连连看游戏**，两个方块消除的条件不仅仅是图案相同，还得保证两个方块之间的最短连线不能多于两个拐点。你玩连连看，点击两个坐标，游戏是如何判断它俩的最短连线有几个拐点的？

再比如……

净整些花里胡哨的，这些问题都没啥奇技淫巧，本质上就是一幅「图」，让你从一个起点，走到终点，问最短路径。这就是 BFS 的本质，框架搞清楚了直接默写就好。

记住下面这个框架就 OK 了：

```java
int DFS(Node start, Node target){
    if (start==target) return 0;
    Deque<Node> q =  new ArrayDeque(); // 队列
    Set<Node> visited = new HashSet(); // 避免走回头路
    
    q.addLast(start);
    visited.add(start);
    int step=0;
    while(!q.isEmpty){
        int size = q.size();
        for(int i=0;i<size;i++){
            Node cur = q.removeFirst();
            if (cur== target){ //找到答案了
                return depth;
            }
            for (Node node: cur.children){ // 将当前点的所有儿子加入到队列中
                if(node not in visited){ //q里面都是未访问过的
                	q.add(node);    
                    visited.add(node); // 记录已经访问过了！
                }
            }
        }
        depth++; // 步数在这里+1；
    }
}

```

在多说几句： 在确定使用DFS后，需要确定这几个因素

1. target， 如：图类一般给定目标点，求树的高度，就是叶子节点；
2. cur.children； 树就是左右叶子，图就是相邻点，二维图就是上下左右移动；

队列q就不说了，BFS 的核心数据结构；cur.adj()泛指cur相邻的节点，比如说二维数组中，cur上下左右四面的位置就是相邻节点；visited的主要作用是防止走回头路，大部分时候都是必须的，但是像一般的二叉树结构，没有子节点到父节点的指针，不会走回头路就不需要visited。



# 二、二叉树的最小高度

先来个简单的问题实践一下 BFS 框架吧，判断一棵二叉树的**最小**高度，这也是 LeetCode 第 111 题，看一下题目：

```java
     3
   9   20
     15  7
最小高度是2；     
     
```

怎么套到 BFS 的框架里呢？首先明确一下起点`start`和终点`target`是什么，怎么判断到达了终点？

**显然起点就是`root`根节点，终点就是最靠近根节点的那个「叶子节点」嘛**，叶子节点就是两个子节点都是`null`的节点：

```java
if (cur.left == null && cur.right ==null)
    // 到达叶子节点
    return depth;
```

那么，按照我们上述的框架稍加改造来写解法即可：

这里希望大家先自己写，在对照答案看看自己哪些出现了问题，下次记得避免；

```java
int minDepth(TreeNode root){
    if (root==null) return 0;
    Deque<TreeNOde> q = new ArrayDeque();
    q.addLast(root);
    int depth =1;// 根节点也算一层
    while(!q.isEmpty){
        int size = q.size();
        for (int i=0;i<size;i++){
            TreeNode cur = q.removeFirst();
            if (cur.left == null && cur.right ==null)// 到达叶子节点
			    return depth;
			if (cur.left != null){
                q.addLast(cur.left);
            }
            if(cur.right != null)
                q.addLast(cur.right);
        }
        depth++;
    }
    return depth;
}
```

二叉树是很简单的数据结构，我想上述代码你应该可以理解的吧，其实其他复杂问题都是这个框架的变形，在探讨复杂问题之前，我们解答两个问题：

**1、为什么 BFS 可以找到最短距离，DFS 不行吗**？

首先，你看 BFS 的逻辑，`depth`每增加一次，队列中的所有节点都向前迈一步，这保证了第一次到达终点的时候，走的步数是最少的。

DFS 不能找最短路径吗？其实也是可以的，但是时间复杂度相对高很多。

你想啊，DFS 实际上是靠递归的堆栈记录走过的路径，你要找到最短路径，肯定得把二叉树中所有树杈都探索完才能对比出最短的路径有多长对不对？

而 BFS 借助队列做到一次一步「齐头并进」，是可以在不遍历完整棵树的条件下找到最短距离的。

形象点说，DFS 是线，BFS 是面；DFS 是单打独斗，BFS 是集体行动。这个应该比较容易理解吧。

**2、既然 BFS 那么好，为啥 DFS 还要存在**？

BFS 可以找到最短距离，但是空间复杂度高，而 DFS 的空间复杂度较低。

还是拿刚才我们处理二叉树问题的例子，假设给你的这个二叉树是满二叉树，节点总数为`N`，对于 DFS 算法来说，空间复杂度无非就是递归堆栈，最坏情况下顶多就是树的高度，也就是`O(logN)`。

但是你想想 BFS 算法，队列中每次都会储存着二叉树一层的节点，这样的话最坏情况下空间复杂度应该是树的最底层节点的数量，也就是`N/2`，用 Big O 表示的话也就是`O(N)`。

由此观之，BFS 还是有代价的，一般来说在找最短路径的时候使用 BFS，其他时候还是 DFS 使用得多一些（主要是递归代码好写）。

好了，现在你对 BFS 了解得足够多了，下面来一道难一点的题目，深化一下框架的理解吧。

# 三、解开密码锁的最少次数

LC752题，题目中描述的就是我们生活中常见的那种密码锁，若果没有任何约束，最少的拨动次数很好算，就像我们平时开密码锁那样直奔密码拨就行了。

但现在的难点就在于，不能出现`deadends`，应该如何计算出最少的转动次数呢？

这题一看就是一个给明起点和终止点的路径题，寻找最短路径的题目；

而且，每一个state下，都知道可以上拨和下拨两种方式,也就是确定了每个点的出路；

直接套框架即可；

优化点：deadends可以直接合并到visited中，因为他们都属于剪枝。

```java
public int openLock(String[] deadends, String target){
    Deque<String> q = new ArrayDeque(); //队列
    q.addLast("0000"); // 起始点
    Set<String> visited = new HashSet(); // 记录访问过的点
    // deadends可以理解为已经访问过的，不能再访问了
    for (String dead: deadends){
        visited.add(dead);
    }
    int step = 0;
    while(!q.isEmpty()){
        int size = q.size();
        for (int i=0;i<size;i++){
            String cur = q.removeFirst();
            if (target.equals(cur)){
                // cur--> parent
                // buildpath(node);
                return step;// 
            }
            for (int j=0;j<4;j++){
                String up = upDail(cur,j);
                if (!visited.contains(up)){
                    q.addLast(up);
                    visited.add(up);
                }
                String down = downDail(cur,j);
                if (!visited.contains(down)){
                    q.addLast(down);
                    visited.add(down);
                }
            }
        }
        step++;
    }
    return -1;
}
private String upDail(String cur,int i){
    char[] arr= cur.toCharArray();
    if (arr[i]=='9'){
        arr[i]='0';
    }else arr[i]+=1;
    return new String(arr);
}
private String downDail(String cur,int i){
    char[] arr= cur.toCharArray();
    if (arr[i]=='0'){
        arr[i]='9';
    }else arr[i]-=1;
    return new String(arr);
}
```



# 四，双向BFS优化

你以为到这里 BFS 算法就结束了？恰恰相反。BFS 算法还有一种稍微高级一点的优化思路：**双向 BFS**，可以进一步提高算法的效率。

篇幅所限，这里就提一下区别：**传统的 BFS 框架就是从起点开始向四周扩散，遇到终点时停止；而双向 BFS 则是从起点和终点同时开始扩散，当两边有交集的时候停止**。

// TODO

待深入





## 五，其他BFS题目

## LC [103. 二叉树的锯齿形层次遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

。。。有些图的联通性问题，并查集 需要在学习







