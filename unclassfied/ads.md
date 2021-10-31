算法

#问题

工作任务安排：

- [x] 优先队列
- [ ] FIFO
- [ ] 栈

扑克游戏：



加密货币：



##如何有效的学习算法和ds

1. chunk it up

2. deliberate practicing
   1. 
3. feedback
   1. 主动性反馈（自己去找）
   2. 被动型反馈（高手指导）

###切题四件套





# 算法题解题思路

1. 若有循环，应保证循环过程的一致性。除非是某种特殊情况。

   ```
   给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。
   
   你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。
   示例:
   
   给定 1->2->3->4, 你应该返回 2->1->4->3.
   
   来源：力扣（LeetCode）
   链接：https://leetcode-cn.com/problems/swap-nodes-in-pairs
   著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
   
   class Solution {
      public ListNode swapPairs(ListNode head) {
               //辅助节点
               ListNode aidNode = new ListNode(0);
               ListNode pre , a, b;
               aidNode.next = head;
               pre = aidNode;
               while (pre.next != null && pre.next.next != null){
                   a = pre.next;
                   b = a.next;
   
                   pre.next = b;
                   a.next = b.next;
                   b.next = a;
                   pre = a;
               }
               return aidNode.next;
           }
   }
   ```

   



## 辅助节点





### 8.6

空数组 new int[0];

双向队列dequeue：ArrayList、ArrayDeque

poll() : 弹出第一个元素

peek()：c查看第一个元素

offer()、add() : 在末尾插入元素

堆

最小堆：PriorityList默认实现

poll(): 弹出堆顶的元素。





### 8.7

比较两个数组是否相等：Arrays.equals(a,b)





###8.8

将list转化成set，直接将list放入构造器中即可。

判断二叉搜索树的一个方法就是中序遍历，看看序列是否是递增的。

递归算法：信任这个递归函数能完成任务。

```java
给定一个二叉树，判断其是否是一个有效的二叉搜索树。
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isValidBST(TreeNode root) {
            return helper(root);
        }
    
         TreeNode pre = null;
        public boolean helper(TreeNode root){
            if (root == null) {
                return true;
            }
            if (!helper(root.left)){
                return false;
            }

            if ( pre!= null && pre.val >= root.val){
                return false;
            }
            pre = root;

            return helper(root.right);

        }
}
```

### 8.9

实现 [pow(*x*, *n*)](https://www.cplusplus.com/reference/valarray/pow/) ，即计算 x 的 n 次幂函数。

使用分治：

```java
public double pow(double x, int n) {
        if (x == 1) {
            return 1;
        }
        //-1 求幂的取值只能为1 或 -1
        if (x == -1) {
            if ((n & 1) != 0) {
                return -1;
            } else {
                return 1;
            }
        }
        if (n == 0) {
            return 1;
        }
        // -2^31 ， 它是负数的最小值，取反后会溢出，
        // 而计算这么多的负次方，它的值无限逼近0，
        // 即使不逼近，double也没那么高的精度，
        // 所以可以认为值是0
        if (n == -2147483648) {
            return 0;
        }
        if (n < 0) {
            return 1 / pow(x, -n);
        }
        if (n % 2 != 0) {
            return pow(x, n - 1) * x;
        }

        return pow(x * x, n / 2);
    }
```

使用迭代：

以 x 的 10 次方举例。10 的 2 进制是 1010，然后用 2 进制转 10 进制的方法把它展成 2 的幂次的和。

$$
x^{10}=x^{(1010)_2}=x^{1*2^3+0*2^2+1*2^1+0*2^0}=x^{1*2^3}*x^{0*2^2}x^{1*2^1}*x^{0*2^0}x 
$$
这样话，再看一下下边的图，它们之间的对应关系就出来了。

> 
>
> ![](D:\ProgramData\md\pow的迭代.png)
>
> 作者：windliang
> 链接：https://leetcode-cn.com/problems/two-sum/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--15/
> 来源：力扣（LeetCode）

```java
public double pow(double x, int n) {
//省略边界条件...
		if (n < 0) {
            x = 1/x;
            n = -n;
        }

        double pow = 1;
        while (n > 0) {
            if ( (n&1) == 1 ){
                pow *= x;
            }
            x *= x;
            n >>= 1;

        }

        return pow;
}
```



求众数

给定一个大小为 *n* 的数组，找到其中的众数。众数是指在数组中出现次数**大于** `⌊ n/2 ⌋` 的元素。

你可以假设数组是非空的，并且给定的数组总是存在众数。

1. 使用map

2. 排序找中位数。

3. 投票竞争法。推荐。
   因为必有一个众数，那么使用候选人、票数这两个变量，众数肯定是比其他数至少多一票的。

   ```
   public int vote(int[] nums) {
           int candidate = nums[0];
           int vote = 1;
           for (int i = 1; i <nums.length ; i++){
               if (vote == 0){
                   candidate = nums[i];
   
               }
   
               if (candidate != nums[i]){
                   vote--;
               }else {
                   vote ++;
               }
   
           }
           return candidate;
       }
   ```

   

### 8.10

### BFS

符合人类思维，按层次遍历，

- 需要使用队列来维护层次的信息。
- 如果是对图进行遍历，还需要一个记录已访问节点的set。

![1565419966162](D:\ProgramData\md\BFS.png)



###DFS

按深度遍历，更符合计算机思维。

![1565420153376](D:\ProgramData\md\DFS递归.png)

![1565420810141](D:\ProgramData\md\DFS非递归.png)

## 剪枝

用于在搜索中做优化。

### set和list的转化

因为List和Set都实现了Collection接口，且都实现了addAll(Collection<? extends E> c)方法，因此可以采用addAll()方法将List和Set互相转换；

另外，List和Set也提供了Collection<? extends E> c作为参数的构造函数，因此通常采用构造函数的形式完成互相转化。

## 位运算

```java
Given a set of distinct integers, nums, return all possible subsets (the power set).

Note: The solution set must not contain duplicate subsets.
Input: nums = [1,2,3]
Output:[[], [1], [2], [1, 2], [3], [1, 3], [2, 3], [1, 2, 3]]
来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/subsets
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        int count = 1 << nums.length;
        for (int i =0; i < count; i++){
            List<Integer> sub = new ArrayList<>();
            for (int j = 0; j < nums.length; j++){
                if ( (i >> j & 1) == 1)
                    sub.add(nums[j]);
            }
            result.add(sub);
        }
        return result;

    }
```

