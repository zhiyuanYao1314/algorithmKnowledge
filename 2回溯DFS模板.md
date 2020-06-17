# 回溯算法 `by Seven`

缩写备注: ``LC46 表示LeetCode第46题`

## 总结

在回溯算法中，尝试完一条路后，我们就回去在尝试其他的路，树状结构的回溯是最常用的。

在回溯的时候，我们需要记录当前的state，也就是当前在哪个位置。

每一个state也是向下层传递的参数，确定state记录哪些元素考虑一下3个目的：

1. 记录**答案路径path和当前的位置**，如记录每一层的选择。
2. 每一层的**可选解空间**。
3. 根据state判断是否达到了**退出条件**，如depth，

框架

```java
result =[];
void dfs(path, 选择列表){
    if (满足推出条件){
        result.add(path);
    	return;
    }       
    for (选择 in 选择列表){
     	做选择;
        dfs(path, 选择列表);
        撤销选择;    
    }
}
    
```

举例：在全排列中，我们需要传递的参数如下：

​	List<Integer> path, 记录每一层的选择，

​	可判断退出条件：depth, 如果depth==n的时候，所有元素选完，退出，实际上，也可以直接根据path的长度判断；

​	每一层的可选解，是求path和nums的差集，但是复杂度较高，可以通过记录一个boolean[], 记录每一个元素是否选择；

## 经典题目目录

下面看一些经典题目：

经典题目目录： 

1. 全排列. LC46

   1. `List<Integer> path`答案路径；
   2. `depth` 结束条件;
   3. `boolean[] used` 每层选取空间;

2. 含有重复元素的全排列，要求结果不重复 LC47：先排序，每一层在可选解空间过滤

   1. `List<Integer> path`答案路径；
   2. `depth` 结束条件;
   3. `boolean[] used` 每层选取空间;
   4. 每层中，需要判断`!used[i-1] && nums[i]== nums[i-1]`, 则减枝；

3. 第k个排列，LC60，需要控制递归找到答案就结束，记录count。

   1. `List<Integer> path`答案路径；
   2. `depth` 结束条件;
   3. `boolean[] used` 每层选取空间;
   4. 每层选取的时候，如果`count==k`， 则剪枝

4. 下一个排列，LC35， 此题不属于回溯方法，但是在排列// 从右往左，找到下降，交换大于，重新排序！

   

5. 组合，位置不同，只能算一次。LC77，可选解空间不同；

   1. `List<Integer> path`答案路径；
   2. path的长度记录结束条件
   3. `int start`记录当前层可以选择的空间, 因为保证下一层，只选择上一层的右边的元素，就保证了不重复；

6. 从数组中找出一些数，使其和等于给定的数--> 组合问题；

   1. `List<Integer> path`答案路径；
   2. sum记录之前层的和； 同时判断==k从而判断结束条件；`or path==nums.length`;
   3. `int start`记录每层的可选解空间；

7. N皇后问题；

   	1. `int[] loc` 记录每一层选择的元素；记录答案；
    	2. `int depth`记录当前遍历到那一层，depth从1开始到n+1就每一层都遍历完，记录答案，结束；
    	3. 每一层的可选解， 通过`int[] loc`和`depth`可以判断，不能垂直和斜对角线，写个check函数；

 8. 在一个矩阵中是否包含某字符串所有字符的路径。`LeetCode`，给定一个二维数组和一个字符串， 

     1. `int s`记录当前到第几个字符串了和当前在数组中的位置,  `if s==len+1` return true;
     2. 通过s判断退出条件，中间如果没有路径，`return false`
     3. 每个元素的可选解空间，上下左右4个方向，不超过数组边界；

 9. 给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。2：A-C, 3:DEF。。。

     1. String 记录当前选择的元素，depth，记录当前的位置
     2. depth从2开始，depth==10结束
     3. 每一层的可选解空间通过全局的`hashmap `和depth确定。

 10. 数组分成n等分；数组有正有负。每一个元素肯定落在n份中的一份中。复杂度`5^n`

      1. 数组`int[n]`记录n份的和；
      2. `k`记录当前的元素个数；k从0开始， k==n，结束；
      3. 可选接空间，就是5个元素；
     
 11.  数独

      1. (x,y)记录当前的位置，path
      2. 可选解1-9，isValid: 横竖，方格不能有相同的；
      3.  退出：先遍历行，if (x==9) (x+1,y); else（x, y+1）；

## 1. 全排列： leetcode46

典形的回溯问题，给定`int[] nums`，求所有的排列`List<List<Integer>>`。

问：怎么确定当前的状态需要记录哪一些值？`path`, `used` 

第一，因为我们需要记录最终选择了哪些元素，所以，需要一个List( java推荐使用Deque)记录我们的path，即每一层选择了哪个元素，然后传下去。

第二，因为当前层不能选取path路径出现过的值，虽然可以通过对比`nums`和`path`来得出当前层的可选解，但是需要遍历一遍`path`，复杂度较高，我们可以取一个变量`used`来记录当前层每一个变量的使用情况，注意：used也需要回溯；

最后，通过path的长度，就可以确定结束条件；

然后，我们可以将最后的答案`List<List<Integer>>`设置为全局变量。

```java
public class Main{
    List<List<Integer>> ans = new ArrayList();
    public static List<List<Integer>> permute(int[] nums){
        // 特殊情况处理
    	if (nums== null || nums.length==0)
            return ans;
        Deque<Integer> path = new ArrayDeque();
        boolean[] used = new boolean[nums.length];
        dfs(nums, path, used);
        return ans;
    }
    // path， 当前路径，used：记录路径的所有已使用值，
    private void dfs(int[] nums,Deque<Integer> path,int[] used){
        // 退出条件
        if (path.size() == nums.length){
            // 需要将path复制一遍作为结果， 因为path记录当前的状态需要一直改变；
            ans.add(new ArrayList(path)); 
            return;
        }
        for (int i=0;i<nums.length;i++){
            if (used[i]){
                continue;
            }
            // 记录当当前state
            used[i] = true;
            path.addLast(nums[i]);
            dfs(nums, path, used); //递归
            // 回溯
            used[i] =false;
            path.removeLast();
        }       
    }
}
```



## 2. 含有重复元素的全排列 `Leetcode 47`

此题和上一题相比，数组存在相同的元素，但是要求最终的解的唯一性。

说明，搜索树的每一层的元素不能相同。

因此，先对数组进行排序，然后每一层的可选解进行去重！

```java

public class Main{
    List<List<Integer>> ans = new ArrayList();
    public List<List<Integer>> permuteUnique(int[] nums){
        if (nums ==null || nums.length==0)
            return ans;
        Deque<Integer> path = new ArrayDeque();
        boolean[] used = new boolean[nums.length];
        Arrays.sort(nums);    
        dfs(nums, path, used);
    }
    private void dfs(int[] nums, Deque<Integer> path, boolean[] used){
        if (path.size()== nums.length){
            ans.add(new ArrayList(path));
        }
        for (int i=0;i<nums.length;i++){
         	if (used[i])  continue;
            // 当前元素和前一个元素相等，且前一个元素未使用过！
            if (i-1>=0 && !used[i-1] &&nums[i+1] == nums[i])
                continue;
            path.addLast(nums[i]);
            used[i] = true;
            dfs(nums, path, used);
			path.removeLast();
            used[i] = false;
        }
    }
}
```



## 3. 第k个排列

给定n表示1---n个数字，求他们的第k个排列。

需要一个count记录当前是第几个，到第k个的时候终止；

注意：需要在每一层的`count==k`的时候，终止；

```java
public class Main{
    String ans;
    int count;
	public String getPermutation(int n, int k) {
        if (n<=0) return ans;
        boolean[] used = new boolean[n];
        dfs(n,"", used, k);
        return ans;
    }
    
    public void dfs(int n, String path, boolean[] used,int k){
        if (path.length()==n){
            count++;
            if (count ==k){
	            ans = path;
            }
            return;
        }
        for (int i=1;i<=n;i++){
			if (count==k) // 提前退出
                return;
            if (used[i-1])
                continue;
			used[i-1] =true;
            path += i;
            dfs(n,path,used, k);
            path = path.substring(0,path.length()-1); 
            used[i-1] = false;
        }
    }
}    
```



## 5. 组合

组合和排列的区别是： 组合是从n个元素中选出k个，排序不同的算同一种组合；

因此，可以设置每向下一层，只能选取右边的元素，这样，就避免了不同的顺序了。



```java
public class Main{
	List<List<Integer>> ans = new ArrayList();
    public List<List<Integer>> combine(int n , int k){
        Deque<Integer> path = new ArrayDeque();
    	dfs(n , 1, path, k); 
        return ans;
    }
    // depth表示当前层可以从第几个元素开始
    private dfs(int n, int depth,Deque<Integer> path, int k){
        if (path.size()==k){
            ans.add(new ArrayList(path));
            return;
        }
        for (int i=depth;i<=n;i++){
            path.addLast(i);
            dfs(n, i+1,path, k);
            path.removeLast();
        }
    } 
}
```



## 6. 从数组中找出一些数，使其和等于给定的数

每一层状态：

​	s, 当前层在数组中开始的位置，sum，进入当前层之前的累加和；

退出条件: sum==target, s已经超出边界

```java
public class Main{
    public boolean sum(int[] arr, int target){
 		if (arr==null || arr.length==0)	   
            return false;
        return dfs(arr, target, 0, 0);
    }
    private boolean dfs(int[] arr, int target, int sum ,int s){
        if (sum== target)
            return true;
        if (s== arr.length+1) 
            return false;
        for (int i=s;i<arr.length;i++){
            sum += arr[i];
            if (dfs(arr, target, sum ,i+1))
                return true;
            sum -= arr[i];
        }
        return false;
    }
}
```



## 7.N皇后问题

皇后不能在横竖和对角线的位置。求所有可能的情况；

每一层放一个皇后，

每一层的状态：

​	boolean[] used 记录当前哪些列放置了Q，

​	int depth， 记录深度，depth ==n+1， 记录答案； 	

```java
public class Main{
    // .Q..
    // ...Q
    // Q...
    // ..Q. 
    
    List<List<String>> ans = new ArrayList();
    public List<List<String>> solveNQueens(int n) {
        if (n<=3) return ans;
        int[] locs = new int[n+1]; //1..n数组中的元素表示每一层放置的皇后的位置；
        dfs(n, 1, locs);
        return ans;
    }

    private void dfs(int n, int depth, int[] locs){
        // 之前的n层已经放置好
        if (depth == n+1){
            List<String> cur = build(locs);
            ans.add(cur);
            return;
        }
        for (int i=1;i<=n;i++){
            if (!check(depth, locs, i)){
                continue;
            }
            locs[depth] = i;
            dfs(n, depth+1, locs);
            locs[depth] = 0;
        }
    }
    // 不能在对角线
    private boolean check(int depth, int[] locs, int i){
        for (int j=1; j<depth;j++){
            if(locs[j]==i) // 当前列有人了！
                return false;
            // j -- locs[j]  3 3
            // depth -- i    4 2
            if (i-locs[j] == depth-j || i-locs[j] == j-depth){
                return false;
            }
        }
        return true;
    }

    // 从1开始
    private List<String> build (int[] locs){
        List ans = new ArrayList();
        for (int i=1;i<locs.length; i++){
            StringBuffer sb = new StringBuffer();
            for (int j=1;j<locs.length; j++){
                if(j==locs[i]){
                    sb.append("Q");
                }else{
                    sb.append(".");
                }
            }
            ans.add(sb.toString());
        }
        return ans;
    }
}
```



## 7. 组合

```java
// 
public combine(int[] nums, k){
   dfs(nums, k, 0, new ArrayList()); 
    return ;
}
List<List<Integer>> ans = new ArrayList();
// 
void dfs(int[] nums, k,int start, List<Integer> path){
    if (path.size()==k){
        ans.add(new ArrayList(path));
        return; 
    }
    for (int i=depth, i<nums.length;i++){
        path.addLast(nums[i]);
		dfs(nums, k, i+1, path);
        path.removeLast(nums[i]);
    }
}

```


