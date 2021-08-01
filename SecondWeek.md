
## 一、LeetCode每日一题记录

#### [671. 二叉树中第二小的节点](https://leetcode-cn.com/problems/second-minimum-node-in-a-binary-tree/)

**示例 1：**

![img](https://uploadfiles.nowcoder.com/files/20210801/154492402_1627798152204/smbt1.jpg)

> 输入：root = [2,2,5,null,null,5,7]
> 输出：5
> 解释：最小的值是 2 ，第二小的值是 5 。

一开始对题意理解错误，看样例以为和二叉搜索树类似，认为第二小的节点在左子树上。

解题思路：

由于要找出第二小的节点值，也就是说当搜索到第一个节点值大于根节点值的节点时就返回，不再往下搜索，所以使用`DFS深度优先搜索`。要注意的是，不存在这个节点值返回-1。

DFS的结束条件如下：

- 当有节点值`node.Val>root.Val`时，给`ans`赋值`ans=node.Val`
- 节点为`nil`或者`ans!=-1`并且当前节点值`node.Val`已经大于`ans`

方法：`DFS深度优先搜索`

时间复杂度：*O*(*n*)，其中 *n* 是二叉树中的节点个数。最多需要对整棵二叉树进行一次遍历。
空间复杂度：*O*(*n*)。使用深度优先搜索的方法进行遍历，需要使用的栈空间为 *O*(*n*)。

```go
func findSecondMinimumValue(root *TreeNode) (ans int) {
    ans=-1                     //首先将ans置为-1，不存在则返回-1
    tmp:=root.Val              //记录根节点值用于比较
    var dfs func(*TreeNode)
    dfs=func(node *TreeNode){  
        if node==nil||ans!=-1&&node.Val>=ans{
            return             //搜索结束的条件
        }
        if node.Val>tmp{
            ans=node.Val
            return
        }
        dfs(node.Left)
        dfs(node.Right)
    }
    dfs(root)
    return
}
```

总结：考察DFS，有待加强，需对DFS进行训练。本题的搜索终止特别要注意。

#### [863. 二叉树中所有距离为 K 的结点](https://leetcode-cn.com/problems/all-nodes-distance-k-in-binary-tree/)

示例 1：

> 输入：root = [3,5,1,6,2,0,8,null,null,7,4], target = 5, K = 2
> 输出：[7,4,1]
> 解释：
> 所求结点为与目标结点（值为 5）距离为 2 的结点，
> 值分别为 7，4，以及 1



![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/06/28/sketch0.png)

本题自己解答错误，参套题解。

解题思路：

- 哈希表存储：要知道距离为K的节点，其中既包含父节点也包含子节点。所以先使用哈希表来存储各个节点的父节点，哈希表的键为每个节点值，这个存储过程是用`DFS`遍历整棵树实现的。
- `DFS`：从`Target`结点出发，`DFS`遍历整棵树。为避免在深度优先搜索时重复访问结点，递归时额外传入来源结点`from`，在递归前比较目标结点是否与来源结点相同，不同的情况下才进行递归。

时间复杂度：*O*(*n*)，其中 *n* 是二叉树中的节点个数。需要执行两次深度优先搜索，每次的时间复杂度均为 O*(*n*)。
空间复杂度：*O*(*n*)。记录父节点需要O*(*n*) 的空间，深度优先搜索需要 O*(*n*) 的栈空间。

```go
func distanceK(root *TreeNode, target *TreeNode, k int) (ans []int) {
    //先用哈希表存储各节点所对应的父节点
    hashTable:=map[int]*TreeNode{}
    var storeParent func(*TreeNode)
    storeParent=func(node *TreeNode){
        if node.Left!=nil{
            hashTable[node.Left.Val]=node
            storeParent(node.Left)
        }
        if node.Right!=nil{
            hashTable[node.Right.Val]=node
            storeParent(node.Right)
        }
    }
    storeParent(root)

    //深度优先搜索
    var findTarget func(*TreeNode,*TreeNode,int)
    findTarget=func(node,from *TreeNode,depth int){     //from表示上一个节点（不只是父节点）
        if node==nil{                                   //node表示当前节点
            return
        }
        if depth==k{
            ans=append(ans,node.Val)
            return
        }
        if node.Left!=from{
            findTarget(node.Left,node,depth+1)
        }
        if node.Right!=from{
            findTarget(node.Right,node,depth+1)
        }
        if hashTable[node.Val]!=from{
            findTarget(hashTable[node.Val],node,depth+1)
        }
    }
    findTarget(target,nil,0)
    return
}

```

#### [1104. 二叉树寻路](https://leetcode-cn.com/problems/path-in-zigzag-labelled-binary-tree/)

![img](https://uploadfiles.nowcoder.com/files/20210801/154492402_1627798151180/tree.png)

> 根据输入节点，输出路径
>
> 输入：label = 14
> 输出：[1,3,4,14]

没有找到数学规律，参考题解。

解题思路：

首先找到标号为 `label` 的节点所在的行和该节点的「从左到右标号」。为了找到节点所在行，需要找到 `i `满足 
$$
2^{i-1} \le \textit{label} < 2^i
$$
 ，则该节点在第 `i` 行。该节点的「从左到右标号」需要根据 ii 的奇偶性计算：

- 当` i`是奇数时，第 ii 行为按从左到右的顺序进行标记，因此该节点的「从左到右标号」即为` label`；

- 当 ii 是偶数时，第 ii 行为按从右到左的顺序进行标记，将整行的标号左右翻转之后得到按从左到右的顺序进行标记的标号，对于同一个节点，其翻转前后的标号之和为 

- $$
  2^{i-1} + 2^i - 1
  $$

  ，因此标号为 label 的节点的「从左到右标号」为 

- 

- $$
  2^{i-1} + 2^i - 1 - \textit{label}
  $$

时间复杂度：`O(loglabel)`。标号为`label` 的节点所在的行数为`O(loglabel)`，因此从根节点到标号label 的节点的路径的长度为 `O(loglabel)`，路径中的每个节点的标号都可以在`O(1)` 时间内计算得到。

空间复杂度：`O(1)`。除了返回值以外，额外使用的空间为常数

```go
func pathInZigZagTree(label int)(path []int) {
    /*根据题解的数学方法：
      将偶数行（即有顺序转换的行）转为原来正常顺序的行
      得到每行对应的label
      奇数行：对应标记路径即为label
      偶数行：对于标记为2^k-1+2^(k-1)-label

      上一行的label为正常二叉树情况下的label/2
    */

    k:=int(math.Log2(float64(label)))
    path=append(path,label)
    label=label/2
    for k>0{
            label=int(math.Pow(2,float64(k))-1+math.Pow(2,float64(k-1))-float64(label))
            path=append(path,label)
            label=label/2
            k--
        
    }
    sort.Ints(path)
    return 
}
```

## 二、数据结构

#### [383. 赎金信](https://leetcode-cn.com/problems/ransom-note/)

**示例 ：**

> 给定一个赎金信 (ransom) 字符串和一个杂志(magazine)字符串，判断第一个字符串 ransom 能不能由第二个字符串 magazines 里面的字符构成。如果可以构成，返回 true ；否则返回 false。
>
> 输入：ransomNote = "aa", magazine = "aab"
> 输出：true

一开始想使用双指针，但还需要先进行排序，麻烦。参考题解使用`hashTable`来存储目标数组得词频，再遍历另一数组对词频进行--操作。

解题思路：若结果为true，则说明`ransomNote`中的字符词频一定和`magazine`中的若干字符词频相同。

1. 可以使用哈希表来存储`magazine`中的所有字符的词频，键为字符，值为词频（即字符个数）。
2. 再去`ransomNote`中遍历取词，遇到一个对应字符就将字符-1，最后若<0，则说明`ransomNote`中对应字符更多，返回false。

时间复杂度：O(n) 

空间复杂度：O(1)

```go
func canConstruct(ransomNote string, magazine string) bool {
    tmp:=[26]int{}
    for _,v:=range magazine{
        tmp[v-'a']++
    }
    for _,v:=range ransomNote{
        tmp[v-'a']--
        if tmp[v-'a']<0{
            return false
        }
    }
    return true
}
```

#### [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

**示例 1：**

![img](https://uploadfiles.nowcoder.com/files/20210801/154492402_1627798151233/circularlinkedlist.png)

> 输入：head = [3,2,0,-4], pos = 1
> 输出：true
> 解释：链表中有一个环，其尾部连接到第二个节点（下标为pos=1）。

朴素办法：

​	根据题目要求：链表中节点的数目范围是 [0, 10^4]，一直遍历

```go
func hasCycle(head *ListNode) bool {
    p:=head
    pos:=0
    if p==nil{
        pos=-1
    }
    for pos<=10001{
        if p!=nil{
            pos++
            p=p.Next
        }else{
            pos=-1
            break
        }
    }
    return pos>-1
}
```

解法二、快慢指针

类似于力扣160（剑指Offer52）两个链表公共节点的问题（双指针），本题使用快慢指针。若为环形链表，则两个指针终究会相遇。

```go
func hasCycle(head *ListNode) bool {
    //双指针（快慢指针）
    
    if head==nil || head.Next==nil{
        return false
    }
    p,q:=head,head.Next
    for p!=q{
        if q==nil || q.Next==nil{
            return false
        }
        p=p.Next
        q=q.Next.Next
        
    }
    return true
```

解法三：

还可以使用哈希表，用以哈希表存储已经遍历过的节点，键为节点，值为pos。若链表节点已经存在哈希表中，则返回true。

#### [203. 移除链表元素](https://leetcode-cn.com/problems/remove-linked-list-elements/)

> 给你一个链表的头节点 `head` 和一个整数 `val` ，请你删除链表中所有满足 `Node.val == val` 的节点，并返回 **新的头节点** 。

![img](https://uploadfiles.nowcoder.com/files/20210801/154492402_1627798152179/removelinked-list.jpg)

解题思路：

迭代遍历链表，当`Node.next.val == val` 时，则`node.next==node.next.next`，否则`node=node.next`

```go
func removeElements(head *ListNode, val int) *ListNode {
    tmp:=&ListNode{Next:head}
    
    for p:=tmp;p.Next!=nil;{
        if p.Next.Val==val{
            p.Next=p.Next.Next
        }else{
            p=p.Next
        }
    }
    return tmp.Next
}
```

#### [20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

**示例 ：**

> 输入：s = "()[]{}"
> 输出：true
>
> 输入：s = "([)]"
> 输出：false

朴素方法：

对每一种情况进行讨论。建立一个数组，把字符串元素依次放到数组中，并且每次放入时都和下一个字符进行比较讨论，若不满足括号要求直接返回false。

```go
func isValid(s string) bool {
    // tmp:=make([]byte,0,len(s))
    var tmp []byte
    tmp=make([]byte,0,len(s))
    if s[0]==')'||s[0]=='}'||s[0]==']'{
        return false
    }
    tmp=append(tmp,s[0])
    for i:=1;i<len(s);i++{
        n:=len(tmp)
        c:=s[i]
        if n==0{
            tmp=append(tmp,c)
        }else if c==')'&&tmp[n-1]=='(' || c=='}'&&tmp[n-1]=='{' || c==']'&&tmp[n-1]=='['{
            tmp=tmp[:n-1]
        }else if tmp[n-1]=='('||tmp[n-1]=='{'||tmp[n-1]=='['&&c=='('||c=='{'||c=='['{
            tmp=append(tmp,c)
        }else{
            return false
        }
    }
    
    if len(tmp)==0 {
        return true
    }
    return false
}
```

正确解题方法：栈

1. 字符串为奇数长度时，必为false；
2. 将右括号为键，左括号为值建立哈希表，一一对应。
3. 遍历字符串，把字符放入栈中。若遇到的是左括号则直接进栈。若为右括号则需要和栈顶元素进行比较，与栈顶不配对或者栈顶为空，false。

```go
func isValid(s string) bool {
    n := len(s)
    if n % 2 == 1 {					//字符串为奇数长度时，必为false；
        return false
    }
    pairs := map[byte]byte{			//将右括号为键，左括号为值建立哈希表，一一对应。
        ')': '(',
        ']': '[',
        '}': '{',
    }
    stack := []byte{}
    for i := 0; i < n; i++ {
        if pairs[s[i]] > 0 {
            if len(stack) == 0 || stack[len(stack)-1] != pairs[s[i]] {
                return false		//右括号则需要和栈顶元素进行比较，与栈顶不配对或者栈顶为空，false。
            }
            stack = stack[:len(stack)-1]
        } else {
            stack = append(stack, s[i])			//遇到的是左括号则直接进栈。
        }
    }
    return len(stack) == 0
}
```

#### [104. 二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

求一棵二叉树的最大深度

解题思路：递归

```go
func maxDepth(root *TreeNode) int {
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
```

#### [101. 对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

给定一个二叉树，检查它是否是镜像对称的。

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

解题思路：递归

如果同时满足下面的条件，两个树互为镜像：

- 它们的两个根结点具有相同的值
- 每个树的右子树都与另一个树的左子树镜像对称

```go
func isSymmetric( root *TreeNode ) bool {
    // write code here
    if root==nil{
        return true
    }
    return dfs(root.Left,root.Right)
}

func dfs(left,right *TreeNode)bool{
    if left==nil&&right==nil{		//两个根结点具有相同的值
        return true
    }
    if left==nil||right==nil{
        return false
    }
    if left.Val!=right.Val{
        return false
    }
    return dfs(left.Right,right.Left)&&dfs(left.Left,right.Right)		//左子树的左子节点和右子树的右子节点相同
}																		//左子树的右子节点和右子树的左子节点相同
```

#### [226. 翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

输入：

     	 4
       /   \
      2     7
     / \   / \
    1   3 6   9

输出：

     	  4
        /   \
       7     2
      / \   / \
     9   6 3   1

解题思路：DFS

类似DFS，在函数中使用DFS的匿名函数，其实这样比较多余，因为不需要返回值而是直接改变树的结构。直接递归就可以。

```go
func invertTree(root *TreeNode) *TreeNode {
    var dfs func(*TreeNode)
    dfs=func(node *TreeNode){
        if node==nil{
            return
        }
        tmp:=node.Left
        node.Left=node.Right
        node.Right=tmp
        dfs(node.Left)
        dfs(node.Right)
    }
    dfs(root)
    return root
}
```

```go
//直接递归
func invertTree(root *TreeNode) *TreeNode {
    if root==nil{
        return nil
    }
    tmp:=root.Left
    root.Left=root.Right
    root.Right=tmp
    invertTree(root.Left)
    invertTree(root.Right)
    return root
}
```

