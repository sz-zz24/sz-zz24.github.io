---
title: "十大排序算法"
date: 2021-03-31T16:01:23+08:00
lastmod: 2021-03-31T16:01:23+08:00
draft: true
tags: ["算法", "排序", "总结"]
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
作为算法中最基础也最重要的一部分，排序是程序员必须要掌握的基础技能之一。

**分类**

排序算法两大类：
* 比较类排序：通过比较来决定元素间的相对次序，由于其时间复杂度不能突破O(nlogn)，因此也称为非线性时间比较类排序。
* 非比较类排序：不通过比较来决定元素间的相对次序，它可以突破基于比较排序的时间下界，以线性时间运行，因此也称为线性时间非比较类排序。

![排序](/blog/2021/sort.png)

排序算法复杂度情况：

![复杂度](/blog/2021/fuzadu.png)

**相关概念**

* **稳定**：如果a原本在b前面，而a=b，排序之后a仍然在b的前面。
* **不稳定**：如果a原本在b的前面，而a=b，排序之后a可能会出现在b的后面。
* **时间复杂度**：对排序数据的总的操作次数。反映当n变化时，操作次数呈现什么规律。
* **空间复杂度**：是指算法在计算机内执行时所需存储空间的度量，它也是数据规模n的函数。

## 1、冒泡排序(Bubble Sort)
冒泡排序是一种简单的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果它们的顺序错误就把它们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

**算法描述**
* 比较相邻的元素。如果第一个比第二个大，就交换它们两个；
* 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数，每进行一次，当前最大的数就会到最后；
* 针对所有的元素重复以上的步骤，除了最后一个；
* 重复步骤1~3，直到排序完成。

**动图演示**
![冒泡排序](/blog/2021/bubble.gif)

**代码实现**
```
func bubbleSort(nums []int) []int {
    for i := 0; i < len(nums)-1; i++ {
        for j := 0; j < len(nums)-1-i; j++ {
            if nums[j] > nums[j+1] {
                nums[j], nums[j+1] = nums[j+1], nums[j]
            }
        }
    }
    return nums
}
```

**算法分析**

冒泡排序在原有的数组上进行，因此空间复杂度是O(1)；最坏情况下(刚好倒序)，每一个数平均需要遍历(n/2)次，因此时间复杂度是O(1/2 n*n)，省略常数，就是O(n*n)；而正常情况下，每个数平均遍历的次数少于(n/2)，但总的时间复杂度依旧是O(n*n)；而最理想的情况是，已经排好序了，只需要遍历一次确认就行了，时间复杂度O(n).

## 2、选择排序(Selection Sort)
选择排序是一种简单直观的排序算法。它的工作原理：首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

**算法描述**

n个记录的直接选择排序可经过n-1趟直接选择排序得到有序结果。具体算法描述如下：

* 初始状态：无序区为R[1..n]，有序区为空；
* 第i趟排序(i=1,2,3…n-1)开始时，当前有序区和无序区分别为R[1..i-1]和R(i..n）。该趟排序从当前无序区中-选出关键字最小的记录R[k]，将它与无序区的第1个记录R交换，使R[1..i]和R[i+1..n)分别变为记录个数增加1个的新有序区和记录个数减少1个的新无序区；
* n-1趟结束，数组有序化了。

**动图演示**
![选择排序](/blog/2021/select.gif)

**代码实现**
```
func selectSort(nums []int) []int {
    minIndex := 0
    for i := 0; i < len(nums)-1; i++ {
        for j := i+1; j < len(nums); j++ {
            if nums[j] < nums[minIndex] {   //寻找最小的数
                minIndex = j          //保存最小的数的索引
            }
        }
        nums[i], nums[minIndex] = nums[minIndex], nums[i]
    }
    return nums
}
```

**算法分析**

选择排序在原有的数组上进行，因此空间复杂度是O(1)；所有情况下，都需要不停地遍历数组，找出最小的元素(即使已经是排好序的)，每一个数平均需要遍历(n/2)次，因此时间复杂度是O(1/2 n*n)，即O(n*n)。

## 6、快速排序(Quick Sort)
快速排序用到了递归分而治之的思想，通过一趟排序将待排记录分隔成独立的两部分，其中一部分记录的关键字均比另一部分的关键字小，则可分别对这两部分记录继续进行排序，以达到整个序列有序。

**算法描述**

快速排序使用分治法来把一个串（list）分为两个子串（sub-lists）。具体算法描述如下：
* 从数列中挑出一个元素，称为 “基准”（pivot）；
* 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；
* 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。

**动图展示**
![快速排序](/blog/2021/quick.gif)

**代码实现**
```
func quickSort(nums []int) []int {
    return recursiveSort(nums, 0, len(nums)-1)
}

func recursiveSort(nums []int, left, right int) []int {
    if left < right {
        partitionIndex := partition(nums, left, right)
        recursiveSort(nums, left, partitionIndex-1)  //递归处理左边小于基准数的部分
        recursiveSort(nums, partitionIndex+1, right) //递归处理右边大于基准数的部分
    }
    return nums
}

func partition(nums []int, left, right int) int {
    //寻找基准数
    index := left + 1
    for i := index; i <= right; i++ {
        if nums[i] < nums[left] { //把小于基准数的数全部放在左边，index指向的是第一个不比基准数小的数字
            nums[i], nums[index] = nums[index], nums[i]
            index++
        }
    } 
    nums[left], nums[index-1] = nums[index-1], nums[left]

    return index - 1
}
```

**算法分析**

快速排序的最坏运行情况是O(n*n)，比如说顺序数列的快排。但它的平摊期望时间是O(nlogn)，且O(nlogn)记号中隐含的常数因子很小，比复杂度稳定等于O(nlogn)的归并排序要小很多。所以，对绝大多数顺序性较弱的随机数列而言，快速排序总是优于归并排序。


## 8、计数排序(Counting Sort)
计数排序不是基于比较的排序算法，其核心在于将输入的数据值转化为键存储在额外开辟的数组空间中。 作为一种线性时间复杂度的排序，计数排序要求输入的数据必须是有确定范围的整数。

**算法描述**
* 确定给出待排序数组的最大值和最小值，初始化一个额外的数组空间k，用来存储待排序数组中的数据和频次
* 遍历待排序数组，统计每个数出现的频次，存入数组k中该数对应的位置，遇到重复的数累加
* 顺序遍历数组k，定义一个从0开始的下标，根据频次把数填充回原数组

**动图演示**
![计数排序](/blog/2021/counting.gif)

**代码实现**
```
func countingSort(nums []int) []int {
    //定义一个额外的数组空间，大小是nums里面最大值
    count := [maxVlue+1]int{}
    for _, v := range nums {
        count[v]++
    }
    sortedIndex := 0
    for k, v := range count {
        for v > 0 {
            nums[sortedIndex] = k
            sortedIndex++
            v--
        }
    }
}
```

**算法分析**

计数排序是一个稳定的排序算法。当输入的元素是n个0到k之间的整数时，时间复杂度是O(n+k)，空间复杂度也是O(n+k)，其排序速度快于任何比较排序算法。当k不是很大并且序列比较集中时，计数排序是一个很有效的排序算法。

**相关题**
* leetcode 1365题：有多少小于当前数字的数字
