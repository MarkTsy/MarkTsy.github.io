---
title: 排序算法
date: 2020-03-13 16:52:59
tags: [排序]
categories: 算法
---

| 排序法   | 最差时间分析     | 平均时间复杂度    | 稳定度 | 空间复杂度         |
| ----- | ---------- | ---------- | --- | ------------- |
| 冒泡排序  | O(n2)      | O(n2)      | 稳定  | O(1)          |
| 快速排序  | O(n2)      | O(n*log2n) | 不稳定 | O(log2n)~O(n) |
| 选择排序  | O(n2)      | O(n2)      | 稳定  | O(1)          |
| 二叉树排序 | O(n2)      | O(n*log2n) | 不一顶 | O(n)          |
| 插入排序  | O(n2)      | O(n2)      | 稳定  | O(1)          |
| 堆排序   | O(n*log2n) | O(n*log2n) | 不稳定 | O(1)          |
| 希尔排序  | O          | O          | 不稳定 | O(1)          |

<!--more-->

#### 冒泡排序:

循环嵌套, 对比相邻两个数, 大的向后移.

```java
 public static void BubbleSort(int[] arr) {
        int temp;//定义一个临时变量
        for(int i=0;i<arr.length-1;i++){//冒泡趟数
            for(int j=0;j<arr.length-i-1;j++){
                if(arr[j+1]<arr[j]){
                    temp = arr[j];
                    arr[j] = arr[j+1];
                    arr[j+1] = temp;
                }
            }
        }
    }
```

#### 选择排序:

循环嵌套, 便利出最大的数, 放置后最后.

```java
public static void selectionSort(int[] list) {
    for (int i = 0; i < list.length - 1; i++) {
        int temp = 0;
        int index = i;
        for (int j = i + 1; j < list.length; j++) {
            if (list[index] > list[j]) {
                index = j;
            }
        }
        temp = list[index];
        list[index] = list[i];
        list[i] = temp;
    }
}
```

#### 插入排序:

把一边的数据看成有序, 将数据不断的移动到应该停止的位置

```java
public static void insertion_sort(int[] unsorted) {
            for (int i = 1; i < unsorted.Length; i++) {
                if (unsorted[i - 1] > unsorted[i]) {
                    int temp = unsorted[i];
                    int j = i;
                    while (j > 0 && unsorted[j - 1] > temp) {
                        unsorted[j] = unsorted[j - 1];
                        j--;
                    }
                    unsorted[j] = temp;
                }
            }
        }
```

#### 快速排序:

选择一个中间值, 移动角标的方式,大的右移小的左移, 递归进行排序.

```java
public static int partition(int []array,int lo,int hi) {
        int key=array[lo];
        while(lo<hi){
            while(array[hi] >= key && hi > lo) {
                hi--;
            }
            array[lo]=array[hi];
            while(array[lo] <= key && hi > lo) {
                lo++;
            }
            array[hi]=array[lo];
        }
        array[hi]=key;
        return hi;
    }
```

#### 二叉树排序:

http://blog.csdn.net/u010853261/article/details/54174609

#### 堆排序:

http://blog.csdn.net/kimylrong/article/details/17150475

#### 希尔排序:

http://blog.csdn.net/jianyuerensheng/article/details/51258460
