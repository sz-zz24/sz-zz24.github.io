---
title: "leetcode面试中级题笔记"
date: 2021-04-15T14:01:23+08:00
lastmod: 2021-04-15T14:01:23+08:00
draft: true
tags: ["算法", "笔记", "总结"]
categories: ["知识总结"]
author: "clavenzhang"

weight: 1

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
# comment: false
# toc: true
mathjax: true
---
## 前言

leetcode面试高频中级题，记录一下思路。


## 题目

1、岛屿的数量，由0和1组成的矩阵，如果四面都是边界或者水，表示这是一个岛屿。给定一个二维矩阵，求岛屿的数量。考察点：深度优先遍历或者广度优先遍历。

解析：将二维矩阵看成无向图，遍历整个二维网络，找到所有能连结的1节点，然后访问过的节点置为2. 这样，0表示海洋节点，1表示陆地节点，2表示已经访问过的陆地节点。深度优先遍历实质上是一种递归方法，一次递归终结之后，表示找到一个岛屿。而终结的条件是：超过了网格范围或者节点是海洋或者节点已经遍历过。
```
//深度遍历  DFS
func numIslands(grid [][]byte) int {
    if len(grid) == 0 {
        return 0
    }
    nr := len(grid)
    nc := len(grid[0])
    count := 0

    //遍历网格，只有是1开始的，才有可能形成连结岛屿
    for i := 0; i < nr; i++ {
        for j := 0; j < nc; j++ {
            if grid[i][j] == '1' { //这个地方要注意一下，不能直接用1，因为数据结构是byte
                count++
                dfs(grid, i, j)
            }
        }
    }

    return count
}

func dfs(grid [][]byte, r, c int) {
    nr := len(grid)
    nc := len(grid[0])

    //节点已经不在网格内
    if !inArea(grid, r, c, nr, nc) {
        return
    }
    //节点已经被遍历过，或者是海洋节点
    if grid[r][c] != '1' {
        return
    }
    //标记当前节点为已经遍历，不然可能形成死循环
    grid[r][c] = '2'
    //递归找网格当中上下左右可连结的陆地节点
    dfs(grid, r-1, c)
    dfs(grid, r+1, c)
    dfs(grid, r, c-1)
    dfs(grid, r, c+1)
}

//判定条件，是否在网格范围内
func inArea(grid [][]byte, r, c, nr, nc int) bool {
    return r >= 0 && r < nr && c >= 0 && c < nc 
}
```
各种岛屿问题的变种，包括岛屿的数量、面积、周长等。都可以用DFS或者BFS的方法解决。

2、给定两个非空的链表，表示两个非负的整数，每位数字都是按照逆序排序，每个节点只能存储一位数字，请将两个数相加，并以相同的形式返回一个表示和的链表。难点：怎么样初始化头节点，以及保住节点指针移动之后的链表。
```
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
    var head, cur *ListNode  //记录结果的链表以及链表指针

    carry := 0   //记录进位
    for l1 != nil || l2 != nil {
        n1, n2 := 0, 0
        if l1 != nil {
            n1 = l1.Val
            l1 = l1.Next
        }
        if l2 != nil {
            n2 = l2.Val
            l2 = l2.Next
        }
        sum := n1 + n2 + carry   //这个地方很巧妙
        sum, carry = sum % 10, sum / 10   //当前位分别为加完之后取余和取整的结果
        //保持head链表完整，同时移动链表指针
        if head == nil {
            head = &ListNode{Val:sum}
            cur = head
        } else {
            cur.Next = &ListNode{Val:sum}
            cur = cur.Next
        }
    }
    //最后加完之后还有进位，在链表末尾补上一个节点
    if carry != 0 {
        cur.Next = &ListNode{Val:carry}
        cur = cur.Next
    }

    return head
}
```

3、每日温度：给定一个气温列表，生成一个新的列表，展示要观测更高温度需要等待的天数。如果气温在之后都不会升高，则用0表示。考察点：栈。具体思路：递减栈，遍历整个数组，如果栈不空，且当前数字大于栈顶元素，就取出栈顶元素，由于当前数字大于栈顶元素的数字，而且一定是第一个大于栈顶元素的数，直接求出下标差就是二者的距离。继续看新的栈顶元素，直到当前数字小于等于栈顶元素停止，然后将数字入栈，这样就可以一直保持递减栈，且每个数字和第一个大于它的数的距离也可以算出来。时间复杂度：O(N) 空间复杂度O(1) 具体代码如下：
```
//递减栈
func dailyTemperatures(T []int) []int {
    n := len(T)
    ans := make([]int, n)   //结果
    stack := []int{}   //用数组表示栈
    for i := 0; i < n; i++ {
        //用for循环得出之前栈里面所有温度上升需要的天数
        for len(stack) != 0 && T[i] > T[stack[len(stack)-1]] {
            //栈保存的是温度的下标，不然没有办法得到下标
            //栈不为空，且当前元素大于栈顶元素
            preIndex := stack[len(stack)-1]
            stack = stack[:len(stack)-1]  //推出栈顶元素
            ans[preIndex] = i - preIndex
        } 
        //栈为空或者元素小于栈顶元素，入栈
        stack = append(stack, i)
    }
    return ans
}
```

扩展题1-下一个更大的元素1：给你两个没有重复元素的数组nums1和nums2，其中nums1是nums2的子集。请你找出nums1中每个元素在nums2中的下一个比其大的值。nums1中数字x的下一个更大元素是指x在nums2 中对应位置的右边的第一个比x大的元素。如果不存在，对应位置输出 -1 。

输入: nums1 = [4,1,2], nums2 = [1,3,4,2]. 输出: [-1,3,-1].

考察点：栈。具体思路：跟上面每日温度的核心思路一致，但是这个题目可能更麻烦一点。因为在全集nums2中可以算出每一个数的下一个更大的元素，还得借助哈希表把这种关系存起来。然后通过遍历第一个数组nums1，根据元素的值从哈希表中找出对应的值。具体代码如下：
```
func nextGreaterElement(nums1 []int, nums2 []int) []int {
    ans := make([]int, len(nums1))
    stack := []int{}  //单调栈
    hashTable := make(map[int]int)
    for i := 0; i < len(nums2); i++ {
        for len(stack) != 0 && nums2[i] > stack[len(stack)-1] {
            //栈顶元素不为空，且当前元素大于栈顶元素
            hashTable[stack[len(stack)-1]] = nums2[i]
            stack = stack[:len(stack)-1]  //栈顶元素出栈
        }
        stack = append(stack, nums2[i])
    }
    for j := 0; j < len(nums1); j++ {
        if hashTable[nums1[j]] == 0 {
            ans[j] = -1
        } else {
            ans[j] = hashTable[nums1[j]]
        }
    }

    return ans
}
```
时间复杂度：O(N+M) 其中NM分别是遍历数组nums2和nums1的时间，空间复杂度：O(N) 单调栈的空间。

扩展题2-下一个更大的元素2：给定一个循环数组（最后一个元素的下一个元素是数组的第一个元素），输出每个元素的下一个更大元素。数字x的下一个更大的元素是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出-1。

输入：nums = [1,2,3,4,3,1] 输出：[2,3,4,-1,4,2]

考察点：栈。具体思路：单调栈是核心，但是这个问题里面数组是循环的，即元素的下一个更大的值可能是前面的元素。那就意味着，需要循环遍历数组，循环遍历数组的做法是，将数组拉直，长度为2n，每个下标对n取余，代码如下：
```
//果然跟想的是一样的，因为遍历一遍数组解决不了问题，所以要把数组整成循环数组
//即单调栈+循环数组
func nextGreaterElements(nums []int) []int {
    n := len(nums)
    ans := make([]int, n)
    stack := []int{}

    for i := 0; i < 2*n-1; i++ {
        if i < n {
            ans[i] = -1
        }
        //栈顶元素不为空，且当前元素大于栈顶元素
        for len(stack) != 0 && nums[i%n] > nums[stack[len(stack)-1]] {
            preIndex := stack[len(stack)-1]
            ans[preIndex] = nums[i%n]
            stack = stack[:len(stack)-1] //出栈
        }
        stack = append(stack, i%n)
    }
    return ans
}
```

时间复杂度: O(N)，其中N是序列的长度。我们需要遍历该数组中每个元素最多2次，每个元素出栈与入栈的总次数也不超过4次。空间复杂度: O(N)，其中N是序列的长度。空间复杂度主要取决于栈的大小，栈的大小至多为2N-1。


4、螺旋矩阵I：给你一个m行n列的矩阵matrix，请按照顺时针螺旋顺序，返回矩阵中的所有元素。考察点：二维数组的高效遍历。具体思路：转圈遍历，分别记录行列的最大、最小值，用于从左到右-->从上到下-->从右到左-->从下到上的转圈遍历，每遍历一个数，元素总数减1，直到元素个数为0.
```
//转圈遍历，时间复杂度O(m*n) 空间复杂度O(1)
func spiralOrder(matrix [][]int) []int {
    if len(matrix) == 0 {
        return nil
    }
    top, bottom, left, right := 0, len(matrix)-1, 0, len(matrix[0])-1
    totalNum := len(matrix)*len(matrix[0])
    var result []int
    for totalNum > 0 {
        //从左到右
        for i := left; i <= right && totalNum > 0; i++ {
            result = append(result, matrix[top][i])
            totalNum--
        }
        //顶向下沉
        top++ 
        //从上向下
        for i := top; i <= bottom && totalNum > 0; i++ {
            result = append(result, matrix[i][right])
            totalNum--
        }
        //右往左移
        right--
        //从右往左
        for i := right; i >= left && totalNum > 0; i-- {
            result = append(result, matrix[bottom][i])
            totalNum--
        }
        //底往上升
        bottom--
        //从下往上
        for i := bottom; i >= top && totalNum > 0; i-- {
            result = append(result, matrix[i][left])
            totalNum--
        }
        //左往右移
        left++
    }
    return result
}
```
这里面有个隐形的陷阱，就是要注意totalNum > 0这个条件要加到里面每一步去。不然的话，长方形的情况下，不足4个元素，right还是大于left，会继续遍历，多出一些元素来。

再看下螺旋矩阵第二题：给你一个正整数n ，生成一个包含1到n*n所有元素，且元素按顺时针顺序螺旋排列的nxn正方形矩阵matrix。解题思路同第一题一样，如果是长方形，一样要考虑多遍历的情况。
```
//转圈遍历
func generateMatrix(n int) [][]int {
    matrix := make([][]int, n)
    num := 1
    top, bottom, left, right := 0, n-1, 0, n-1
    for i := range matrix {
        matrix[i] = make([]int, n)
    }
    
    for num <= n * n {
        //从左到右
        for i := left; i <= right; i++ {
            matrix[top][i] = num
            num++
        }
        //顶向下沉
        top++
        //从上向下
        for i := top; i <= bottom; i++ {
            matrix[i][right] = num
            num++
        }
        //右向左移
        right--
        //从右往左
        for i := right; i >= left; i-- {
            matrix[bottom][i] = num
            num++
        }
        //底向上移
        bottom--
        for i := bottom; i >= top; i-- {
            matrix[i][left] = num
            num++
        }
        //左向右移
        left++
    }
    return matrix
}
```
这里面要注意的问题是golang里面对slice的支持不是特别友好，二维数组的初始化。以及，如果是长方形的话，每个条件都要加上num <= n*n的条件。

5、三数之和：给你一个包含n个整数的数组nums，判断nums中是否存在三个元素a，b，c，使得 a + b + c = 0 ？请你找出所有和为0且不重复的三元组。注意：答案中不可以包含重复的三元组。解决方法：数组排序+双指针。具体思路：边界判断，小于3个数的，直接返回。先将数组升序排序，如果不排序，就得按照暴力的思路，三个for循环。然后定义左右两个指针，分别指向遍历数组的当前数之后的最小值和最大值。然后滑动指针，找出遍历当前元素下，所有和等于0的三元组，直到左指针和右指针相遇。具体思路见代码步骤详细说明。

```
//排序 + 双指针
func threeSum(nums []int) [][]int {
    result := make([][]int, 0)
    n := len(nums)
    if n < 3 {
        return result
    }
    //先对数组进行排序，内置的sort函数会根据输入选择最合适的排序算法，应该是快排
    sort.Slice(nums, func(i,j int) bool {
        return nums[i] < nums[j]
    })
    //定义两个指针，分别指向遍历当前元素后面的一位即i+1和数组最大值n-1
    for i := 0; i < n; i++ {
        //已经排好序的，如果最小值都大于0，直接不用看后面的了
        if nums[i] > 0 {
            return result
        }
        //跳过重复元素，避免生成重复解
        if i > 0 && nums[i] == nums[i-1] {
            continue
        }
        left, right := i + 1, n - 1
        //边界条件，如果left大于right，就重复遍历了，等于的话，不足三个数
        for left < right {
            if nums[i] + nums[left] + nums[right] == 0 {
                result = append(result, []int{nums[i], nums[left], nums[right]})
                //跳过重复的元素，左右指针同理
                for left < right && nums[left] == nums[left+1] {
                    left++
                }
                for left < right && nums[right-1] == nums[right] {
                    right--
                }
                //这个地方一开始有困扰，为啥两个指针都移动。原因是，如果左指针移动了，因为是排好序且不重复的，则意味着单移动一个指针，肯定达不到结果为0的情况。
                //即左数加大了，右数必须要减小才行。
                left++
                right--
            } 
            if left < right && nums[i] + nums[left] + nums[right] > 0 {
                right--
            }
            if left < right && nums[i] + nums[left] + nums[right] < 0 {
                left++
            }
        }
    }
    return result
}
```