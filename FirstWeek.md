# 每周力扣刷题情况记录（一）
记录小白每周的刷题情况

**一、LeetCode每日一题记录**
***1. 力扣1877数组中最大数对和的最小值***

> 给你一个长度为 偶数 **n** 的数组 **nums** ，请你将 **nums** 中的元素分成 **n / 2** 个数对，使得：

> **nums** 中每个元素恰好在一个数对中，且最大数对和的值最小。

> 请你在最优数对划分的方案下，返回**最小的**最大数对和 。

> 示例1：

>  输入：nums = [3,5,2,3]

>  输出：7

>  解释：数组中的元素可以分为数对 (3,3) 和 (5,2) 。

>  最大数对和为 max(3+3, 5+2) = max(6, 7) = 7 。

解答思路：

根据题意，将数组中的元素两两生成一个数对，要使得这个最大的数对的和最小，那么就要做到这个数组中的第k大的与第k小的组成一个数对（尽可能地平均分配）。

方法：**排序+贪心**

时间复杂度：**O(nlogn)**，排序**nums**的时间复杂度为**O(nlogn)**，遍历维护最大数对和的时间复杂度为 **O(n)**。

空间复杂度：**O(logn)**，排序的栈空间开销

```
func minPairSum(nums []int) int {
    /*
    思路：
    1.先将数组排序；
    2.排序后数组中的最小值+最大值即(0,n-1) (1,n-2)...
    */
    res:=0
    n:=len(nums)
    sort.Ints(nums)
    for i:=0;i<n/2;i++{
        if nums[i]+nums[n-1-i]>res{
            res=nums[i]+nums[n-1-i]
        }
    }
    return res
}
```


***2.剑指Offer 52两个链表的第一个公共节点（同力扣160题）***

解答思路：
链表公共节点，即所指向的为同一个节点的地址。

若存在公共节点，那么设两链表的公共长度为L，非公共部分的长度为M，N。（第一条链表长为L+M，第二条为L+N）。
两链表若要走过的相同长度，从头节点开始到公共节点相交，不难得到长度为L+M+N。所以，一条链表走完后直接从另一条链表头部继续走下去，直至到公共节点相交即可。
若两者不存在公共节点，那么最后走完后都是NULL，不影响结果。

方法：**双指针**

时间复杂度：**O(m+n)**

空间复杂度：**O(1)**

```
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func getIntersectionNode(headA, headB *ListNode) *ListNode {
    if headB==nil || headA==nil{
        return nil
    }
    pA,pB:=headA,headB
    for pA!=pB{
        if pA!=nil{
            pA=pA.Next
        }else{
            pA=headB
        }
        if pB!=nil{
            pB=pB.Next
        }else{
            pB=headA
        }
    }
    return pA
}
```

***3.力扣1893检查是否区域内所有整数都被覆盖***

> 示例 1：

> 输入：ranges = [[1,2],[3,4],[5,6]], left = 2, right = 5

> 输出：true

> 解释：2 到 5 的每个整数都被覆盖了：

> - 2 被第一个区间覆盖。
> - 3 和 4 被第二个区间覆盖。
> - 5 被第三个区间覆盖


解答思路：

暴力解法：根据题意进行模拟，遍历[left,right]之间每一个整数是否在ranges数组中。如果其中有一个没被覆盖，那就直接false+break。

时间复杂度：**O(m*n)**。m，n分别表示ranges数组长度，[left,right]之间的整数个数。

空间复杂度：**O(1)**。

```
func isCovered(ranges [][]int, left int, right int) bool {
    for x:=left;x<=right;x++{
        ok:=false
        for _,cur:=range ranges{
            if x<=cur[1]&&x>=cur[0]{
                ok=true
                break
            }
        }
        if !ok{
            return false
        }           
    }
    return true  
}
```


**注、**

**本题其他更优的解法：差分数组、线段树。需要对这两种算法进行了解学习**


**二、数据结构训练**

**1.力扣350两个数组的交集 II**

> 给定两个数组，编写一个函数来计算它们的交集。

> 示例 :

> 请在这里输入引用内容

> 输入：nums1 = [4,9,5], nums2 = [9,4,9,8,4]

> 输出：[4,9]

解题思路：

一开始还是想用暴力解法，两个数组双层遍历比较，但是会出现重复结果的情况。
换一种想法，先对数组进行排序，再用双指针对两数组的元素逐一比较。

方法：**排序+双指针**

时间复杂度：O(mlogm+nlogn)

空间复杂度：O(min(m,n))

```
func intersect(nums1 []int, nums2 []int) (res []int) {
    n:=len(nums1)
    m:=len(nums2)
    i,j:=0,0
    sort.Ints(nums1)
    sort.Ints(nums2)
    for i<n&&j<m{      
        if nums1[i]==nums2[j]{
            res=append(res,nums1[i])
            i++
            j++
        }else if nums1[i]<nums2[j]{
            i++
        }else{
            j++
        }
    }
    return
}
```

**注、**

**本题其他解法：哈希表，键表示一个数组中的元素，值表示该元素在数组中出现的次数，创建hashTable。遍历另一数组，判断另一数组元素是否在哈希表键中出现，若出现一次就将对应的值减1，直到减至0**

**2.力扣118杨辉三角**

解题思路：

杨辉三角的一个数学性质：

> 第i行的第j项=第i-1行的第j-1项+第i-1行的第j项。

> nums[i][j]=nums[i-1][j-1]+nums[i-1][j]

利用这一性质进行解题。首先，遍历行：每一行的第一项（j=0）和最后一项（j=i)都=1，接着j=1开始，第i行的第j项=第i-1行的第j-1项+第i-1行的第j项。

```
func generate(numRows int) [][]int {
    res:=make([][]int,numRows)
    for i,_:=range res{
        res[i]=make([]int,i+1)
    }
    for i:=0;i<numRows;i++{
        res[i][0]=1
        res[i][i]=1
        for j:=1;j<i;j++{
            res[i][j]=res[i-1][j-1]+res[i-1][j]
        }
    }
    return res
}
```

其余题目简述：

1、

566.重塑矩阵，根据二维数组的一维表示，对于**m*n**的二维数组，存在如下映射：

二维数组的第x个数（x从0开始），**x=i*n+j**。

重塑成r*c的数组就是：**res[x/c][x%c]=mat[x/n][x%n]**

2、

36.有效数独，我的做法使用三次迭代来判断行、列和各九宫格内的数字重复情况。

但是按照官方题解可以使用一次迭代就能完成。使用数组嵌套哈希表来求解，一组个哈希表来作为一个数组元素，遍历行/列/九宫格内元素是否在哈希表中已存在，不存在就添加进哈希表，已存在就返回false。

3、

73.矩阵置零，我的做法使用一个临时二维切片tmp（复制原切片得到），遍历原切片得到元素为0的下标，然后将tmp中的对应下标的行与列元素都替换成0，最后把tmp复制给原切片返回。

这种方法比较复杂，并且在声明临时切片时，要注意二维数组复制原切片时，要在内层复制，这样对tmp进行修改时才不会更改原切片matrix，即如下代码：

```
tmp:=make([][]int,m)
    for i,_:=range tmp{
        tmp[i]=make([]int,n)
        copy(tmp[i],matrix[i])   //要在内存进行复制，matrix为原切片
    }
    // copy(tmp,matrix)          //外层复制没有用，修改tmp会同时把matrix也修改了。
```

**本周总结：**

**1.对于哈希表的运用还不够灵活，需继续加深练习。**

**2.对于两个数组的排序的题目，考虑使用双指针、归并排序等方法。**

**3.力扣1893检查是否区域内所有整数都被覆盖的题目，要对差分数组、线段树两个算法进行学习了解。**
