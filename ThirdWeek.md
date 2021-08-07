# 每周力扣刷题情况记录（三）

## 一、二叉树

#### [653. 两数之和 IV - 输入 BST](https://leetcode-cn.com/problems/two-sum-iv-input-is-a-bst/)

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/09/21/sum_tree_1.jpg)

```go
输入: root = [5,3,6,2,4,null,7], k = 9
输出: true
```

我的思路：朴素解法。

1. 中序遍历得到一个非递减序列
2. 对非递减序列进行双层`for`循环遍历，判断`x+y==k`

时间复杂度：O(n^2),双层遍历                                                             空间复杂度：O(n)

参考题解：中序遍历得到有序数组以后，使用**前后双指针**。首尾元素求和与k比较，并逐渐往中间靠拢。

```go
func findTarget(root *TreeNode, k int) (flag bool) {
    flag=false
    ans:=make([]int,0,10001)
    var midOrder func(*TreeNode)
    midOrder=func(node *TreeNode){
        if node==nil{
            return
        }
        midOrder(node.Left)
        if root!=nil{
            ans=append(ans,node.Val)
        }
        midOrder(node.Right)
        return
    }
    midOrder(root)
    //前后双指针
    l,r:=0,len(ans)-1
    for l<r{
        if ans[l]+ans[r]==k{
            flag=true
            break
        }
        if ans[l]+ans[r]<k{
            l++
        }else{
            r--
        }
    }
    return
}
```

#### [101. 对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

> 判断一棵二叉树是否镜像对称。

参考题解：递归。

镜像对称，说明左右节点的值要相同**并且**左子树与右子树镜像对称（左左==右右，左右=右左）

时间复杂度：O(n)							                                                            空间复杂度：O(n)

```go
func isSymmetric(root *TreeNode) bool {
    if root==nil||root.Left==nil&&root.Right==nil{
        return true
    }
    return check(root.Left,root.Right)
}

func check(left,right *TreeNode)bool{
    if left==nil&&right==nil{
        return true
    }
    if left!=nil&&right==nil||right!=nil&&left==nil{
        return false
    }
    
    //if left.Val==right.Val{	不能单独拎出left.Val==right.Val判断，
    //    return true			要注意和check(left.Left,right.Right)&&check(left.Right,right.Left)是“与”关系
    //}
    
    return left.Val==right.Val&&check(left.Left,right.Right)&&check(left.Right,right.Left)
}
```

#### [111. 二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

> 求一个二叉树的最小深度，要注意根节点没有单侧子树的情况。

思路：递归，深搜DFS。

时间复杂度：O(n)						                                                           空间复杂度：O(H)，H为树高

```go
func minDepth(root *TreeNode) int {
    if root==nil{
        return 0
    }
    if root.Left==nil&&root.Right!=nil{
        return minDepth(root.Right)+1
    }
    if root.Right==nil&&root.Left!=nil{
        return minDepth(root.Left)+1
    }
    if root.Left==nil&&root.Right==nil{
        return 1
    }
    return min(minDepth(root.Left),minDepth(root.Right))+1
}

func min(a,b int)int{
    if a<b{
        return a 
    }
    return b
}
```

#### [222. 完全二叉树的节点个数](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

> 完全二叉树 的定义如下：
>
> 在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 h 层，则该层包含 1~ 2h 个节点。

思路：层序遍历二叉树，放入数组，求数组长。

题解：递归，后序遍历。

时间复杂度：O(n)						                                                           空间复杂度：O(logn)，算上了递归系统栈占用的空间

```go
func countNodes(root *TreeNode) int {
    if root==nil{
        return 0
    }
    res:=1
    if root.Left!=nil{
        res+=countNodes(root.Left)
    }
    if root.Right!=nil{
        res+=countNodes(root.Right)
    }
    return res
}
```

#### [110. 平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)

一棵高度平衡二叉树定义为：

> 一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过 1 。

 题解：

计算两棵子树的深度，比较是否超过1。  接着递归

时间复杂度：O(n^2)						                                                           空间复杂度：O(n)

```go
func isBalanced(root *TreeNode) bool {
    if root==nil{
        return true
    }
    return abs(maxDepth(root.Left)-maxDepth(root.Right))<=1&&isBalanced(root.Right)&&isBalanced(root.Left)
}
//深度计算的函数
func maxDepth(root *TreeNode)int{
    if root==nil{
        return 0
    }
    return max(maxDepth(root.Left),maxDepth(root.Right))+1
}

func max(a,b int)int{
    if a>b{
        return a
    }
    return b
}
//绝对值计算
func abs(x int)int{
    if x<0{
        return -1*x
    }
    return x
}

```

#### [106. 从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

> 已知一棵树的中序和后续遍历数组，构造出原二叉树

思路：

1. 已知后序遍历，可以根据后序遍历的序列得到二叉树的根节点			

   ```
            后序遍历的最后一位就是根节点
   ```

2. 根据后序遍历得到根节点，将中序遍历分为左右两部分（各代表左右子树）；再根据中序遍历分到的左右两子树，可知后序遍历的前n项即为中序左子树的n项；

3. 中序、后序被分成左右两子树后，再去后序遍历中去根节点，递归。

时间复杂度：O(n)						                                                           空间复杂度：O(n)

```go
func buildTree(inorder []int, postorder []int)  *TreeNode {
    if len(inorder)<1||len(postorder)<1{return nil}
    m:=len(postorder)
    rootVal:=postorder[m-1]
    root:=&TreeNode{Val:rootVal}		//后序取根节点
   
    var i int
    for i=0;i<len(inorder);i++{			//去中序找到分割左右子树的下标
        if inorder[i]==rootVal{
            break
        }
    }
    
    postorder=postorder[:m-1]			//处理后序遍历，取一个根节点就删除一个根节点
    //对中序、后序进行分割
    postLeft,postRight:=postorder[0:i],postorder[i:]
    inLeft,inRight:=inorder[0:i],inorder[i+1:]
    //递归
    root.Right=buildTree(inRight,postRight)
    root.Left=buildTree(inLeft,postLeft)
    return root
}
```

#### [654. 最大二叉树](https://leetcode-cn.com/problems/maximum-binary-tree/)

```
以最大值为根节点，最大值左侧放在左子树，右侧放在右子树。
输入：nums = [3,2,1,6,0,5]
输出：[6,3,5,null,2,0,null,null,1]
```

思路：递归。

1. 使用哈希表存储各元素机器下标
2. 在数组中找到最大值，并将其分割
3. 左右两侧递归，分别赋值给左右子树

```go
func constructMaximumBinaryTree(nums []int) *TreeNode {
    if len(nums)<1{
        return nil
    }
    //存入哈希表
    idxMap:=map[int]int{}
    for i,num:=range nums{
        idxMap[num]=i
    }
    //排序取最大值
    a:=make([]int,len(nums))
    copy(a,nums)
    sort.Ints(a)
    //根据最大值的下标，分割数组
    rootVal:=a[len(a)-1]
    k:=idxMap[rootVal]
    leftNums,rightNums:=nums[0:k],nums[k+1:]
    
    root:=&TreeNode{
        Val:rootVal,
        Left:constructMaximumBinaryTree(leftNums),
        Right:constructMaximumBinaryTree(rightNums),
    }

    return root
}
```



## 二、数组

#### [136. 只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

**示例 1:**找出数组中只出现一次的数字，不适用额外空间。

```
输入: [2,2,1]
输出: 1
```

思路：使用哈希表存储词频，统计数字出现的次数。但是题目要求不适用额外的空间来实现。

题解：位运算。使用异或运算符（相异为0，相同为1），对各个数字进行异或运算。

时间复杂度：O(n)                                                                                   空间复杂度：O(1)

```go
func singleNumber(nums []int) int {
  sum:=0
  for _,num:=range nums{
​    sum=sum^num
  }
  return sum
}
```

#### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

**此题需要关注**

**示例 1：**判断数组中是否存在三个元素何为0？

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

参考题解：

1. 对数组进行非递减排序；
2. 对数组进行遍历，取第一个数字为`a`，在`a`后的数组中使用双指针取`b`、`c`；
3. 判断`b+c==-a`

时间复杂度：O(N^2)																				空间复杂度：O(logN)。这里是排序的空间复杂度为 O(logN)。此揭发修改了输入的数组 nums，在实际情况下不一定允许，因此也可以看成使用了一个额外的数组存储了 nums 的副本并进行排序，空间复杂度为O(N)。

```go
func threeSum(nums []int)  [][]int {
    if len(nums)<3{
        return nil
    }
    
    n:=len(nums)
    sort.Ints(nums)
    ans:=make([][]int,0)

    for i:=0;i<n;i++{
        // 需要和上一次枚举的数不相同
        if i>0&&nums[i]==nums[i-1]{
            continue
        }
        target:=-1*nums[i]
        // c 对应的指针初始指向数组的最右端
        k:=n-1
        // 枚举 b
        for j:=i+1;j<n;j++{
            // 需要和上一次枚举的数不相同
            if j>i+1&&nums[j]==nums[j-1]{
                continue
            }
            // 需要保证 b 的指针在 c 的指针的左侧
            for j<k&&nums[j]+nums[k]>target{
                k--             //先k--再比较是否相等，若放到最后k--会接着j++，这样会忽略一种情况
            }
            // 如果指针重合，随着 b 后续的增加
            // 就不会有满足 a+b+c=0 并且 b<c 的 c 了，可以退出循环
            if j==k{
                break
            }
            if nums[j]+nums[k]==target{
                ans=append(ans,[]int{nums[i],nums[j],nums[k]})
            }
            
        }
    }
    return ans
}

```

#### [169. 多数元素](https://leetcode-cn.com/problems/majority-element/)

**示例 1：**找出数组中个数大于`n/2`的元素

```
输入：[3,2,3]
输出：3
```

思路：哈希表存储词频遍历哈希表求解。

题解：

方法一：摩尔排序法。

1. 从第一个数作为`signal`开始`count=1`，
2. 遇到相同的就加1，遇到不同的就减1，
3. 减到0就重新换当前数作为`signal`开始计数，计数重置为`count=1`，返回最终的`signal`

时间复杂度：O(n)                                                                                   空间复杂度：O(1)

```go
func majorityElement(nums []int) (ans int) {
    //摩尔排序
    count:=1
    signal:=nums[0]
    for i:=1;i<len(nums);i++{
        if count==0{
            signal=nums[i]
            count++
        }else if signal!=nums[i]{
            count--
        }else{
            count++
        }
    }
    ans=signal
    return
}
```

方法二：对数组非递减排序，取下标为`n/2`的数即为返回值。

时间复杂度：O(n*logn)                                                                                   空间复杂度：O(logn)

```go
func majorityElement(nums []int) (ans int) {
    //排序，返回第n/2下标的数必是多数
    sort.Ints(nums)
    return nums[len(nums)/2]
}
```

#### [75. 颜色分类](https://leetcode-cn.com/problems/sort-colors/)

使用整数 `0`、 `1` 和 `2` 分别表示红色、白色和蓝色。对三种颜色进行红白蓝（0->1->2）排序。

**示例 1：**

```
输入：nums = [2,0,2,1,1,0]
输出：[0,0,1,1,2,2]
```

思路：

1. 直接使用sort包；**可以不使用代码库中的排序函数来解决这道题吗？**

2. 使用额外数组；**能否使用常数空间的一趟扫描算法？**

   1. 在数组中第i下标处插入元素的方法

      ```go
      nums=append(nums,num)
      copy(nums[1:],nums[0:])            
      nums[0]=num
      ```

题解：单指针

```
从左向右遍历整个数组，如果找到了 0，那么就需要将 0 与「头部」位置的元素进行交换，并将「头部」向后扩充一个位置。在遍历结束之后，所有的 0 都被交换到「头部」的范围，并且「头部」只包含 0。
```

```go
func sortColors(nums []int)  {
    a:=changeColors(nums,0)
    changeColors(nums[a:],1)
}

func changeColors(nums []int,target int)(count int){
    for i,num:=range nums{
        if num==target{
            nums[i],nums[count]=nums[count],nums[i]
            //元素交换操作
            count++
        }
    }
    return
}
```

#### [56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/)

**示例 1：**将重叠的区间合并

```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
```

参考题解：

1. 使用`sort.Slice()`对二位切片中的每个切片元素进行排序，按每个内层切片的首元素非递减排序。
2. 遍历排序后的二维数组，比较前一项的右端点与当前项的左端点的大小关系。
3. 取`pre=intervals[0]`，若前一项的右端点>=当前项的左端点，则`pre[1]=max(pre[1],next[1])`，`pre`右端点不变并继续遍历下一项；若前一项的右端点<当前项的左端点，则`pre=next`（当前项变为被比较项，与后一项进行比较）

时间复杂度：O(n*logn)                                                                                   空间复杂度：O(logn)

```go
func merge(intervals [][]int) (ans [][]int) {
    sort.Slice(intervals,func(i,j int)bool{
		return intervals[i][0]<intervals[j][0]
	})
	
	pre:=intervals[0]
	for i:=1;i<len(intervals);i++{
		next:=intervals[i]
		if pre[1]>=next[0]{
			pre[1]=max(pre[1],next[1])
		}else{
			ans=append(ans,pre)
			pre=next
		}
	}
    ans=append(ans,pre)
	return 
}

func max(a,b int)int{
    if a>b{
        return a
    }
    return b
}
```

