---
title: 程序员常用算法
date: 2019-12-14 02:48:04
categories:
 - 算法
tags:
 - 算法
 - 排序算法
 - 基础算法
 - 算法实例
---


## 算法

> 算法（Algorithm）是指解题方案的准确而完整的描述,是一系列解决问题的清晰指令,算法代表着用系统的方法描述解决问题的策略机制.
> 也就是说,能够对一定规范的输入,在有限时间内获得所要求的输出.  
> 如果一个算法有缺陷,或不适合于某个问题,执行这个算法将不会解决这个问题.  
> 不同的算法可能用不同的时间、空间或效率来完成同样的任务.  
> 一个算法的优劣可以用空间复杂度于时间复杂度来衡量
> ----维基百科

### 插入排序

> 有一个已经有序的数据序列，要求在这个已经排好的数据序列中插入一个数
> 但要求插入后此数据序列仍然有序，这个时候就要用到一种新的排序方法——插入排序法
> 插入排序的基本操作就是将一个数据插入到已经排好序的有序数据中
> 从而得到一个新的、个数加一的有序数据，算法适用于少量数据的排序
> 时间复杂度为 O(n^2)。是稳定的排序方法。插入算法把要排序的数组分成两部分：
> 第一部分包含了这个数组的所有元素，但将最后一个元素除外（让数组多一个空间才有插入的位置），
> 而第二部分就只包含这一个元素（即待插入元素）。
> 在第一部分排序完成后，再将这个最后元素插入到已排好序的第一部分中。
> 插入排序的基本思想是：每步将一个待排序的记录
> 按其关键码值的大小插入前面已经排序的文件中适当位置上，直到全部插入完为止。

排序过程步骤:

1. 从第一个元素开始, 该元素可以认为已经被排序  
2. 取出下一个元素, 在已经排序的元素序列中从后向前扫描  
3. 如果该元素(已排序)大于新元素,将该元素移到下一位置
4. 重复步骤3, 直到找到已排序的元素小于或者等于新元素的位置  
5. 将新元素插入到该位置后  
6. 重复步骤2~5  

示例代码:

``` 
import java.util.Arrays;

public class InsertSort {
    public static void sort(int[] arr) {
        int temp;
        for (int i = 1; i < arr.length; i++) {
            for (int j = 0; j < i; j++) {
                //对已经排序好的元素比较，找到一个比插入元素大的元素 交换位置
                if (arr[i] < arr[j]) {
                    temp = arr[i];
                    arr[i] = arr[j];
                    arr[j] = temp;
                }
            }
        }
    }

    public static void main(String[] args) {
        int[] ints = {5, 3, 4, 1, 2};
        sort(ints);
        System.out.println(Arrays.toString(ints));
    }
}
```

### 冒泡排序

> 冒泡排序（Bubble Sort），是一种计算机科学领域的较简单的排序算法。 它重复地走访过要排序的元素列 
> 依次比较两个相邻的元素，如果他们的顺序（如从大到小、首字母从 A 到 Z）错误就把他们交换过来
> 走访元素的工作是重复地进行直到没有相邻元素需要交换，也就是说该元素已经排序完成-
> 这个算法的名字由来是因为越大的元素会经由交换慢慢“浮”到数列的顶端（升序或降序排列），就如同碳酸饮料中二氧化碳的气泡最终会上浮到顶端一样 
> 故名“冒泡排序”。

冒泡排序的运行过程如下:

* 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
* 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。
* 针对所有的元素重复以上的步骤，除了最后一个。
* 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较 

示例代码:

``` 
import java.util.Arrays;

public class BubbleSort {
    public static void sort(int[] arr) {
        for (int i = 0; i < arr.length-1; i++) {
            for (int j = 0; j < arr.length - i - 1; j++) {
                //如果当前元素比后一位元素大 交换位置
                if (arr[j] > arr[j + 1]) {
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                }
            }
        }
    }

    public static void main(String[] args) {
        int[] ints = {5, 3, 4, 1, 2};
        sort(ints);
        System.out.println(Arrays.toString(ints));
    }
}
```

### 归并排序 

> 归并排序（MERGE-SORT）是建立在归并操作上的一种有效的排序算法
> 该算法是采用分治法（Divide and Conquer）的一个非常典型的应用
> 将已有序的子序列合并，得到完全有序的序列；
> 即先使每个子序列有序，再使子序列段间有序。
> 若将两个有序表合并成一个有序表，称为二路归并。

排序过程:

1. 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列
2. 设定两个指针，最初位置分别为两个已经排序序列的起始位置
3. 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置
4. 重复步骤 3 直到某一指针到达序列尾
5. 将另一序列剩下的所有元素直接复制到合并序列尾

``` 
import java.util.Arrays;

public class MergeSort {

    public static void mergeSort(int[] arrays, int left, int right) {
//        如果数组还可以拆分
        if (left < right) {
            //数组的中间位置
            int middle = (left + right) / 2;
            //拆分左边数组
            mergeSort(arrays, left, middle);
            //拆分右边数组
            mergeSort(arrays, middle + 1, right);
            //合并
            merge(arrays, left, middle, right);

        }
    }


    /**
     * 合并数组
     */
    public static void merge(int[] arr, int left, int middle, int right) {
        //申请合并空间 大小为两个已经排序序列之和
        int[] temp = new int[right - left + 1];
        //i 和 j为两个已经排好序的数组的起始位置
        int i = left;
        int j = middle + 1;
        int k = 0;
        //排序
        while (i <= middle && j <= right) {
            //将比较小的数组放入合并空间
            if (arr[i] < arr[j]) {
                temp[k++] = arr[i++];
            } else {
                temp[k++] = arr[j++];
            }
        }
        //将左边剩余元素写入合并空间
        while (i <= middle) {
            temp[k++] = arr[i++];
        }
        //将右边剩余的元素写入合并空间
        while (j <= right) {
            temp[k++] = arr[j++];
        }
        //将排序后的数组写回原来的数组
        for (int l = 0; l < temp.length; l++) {
            arr[l + left] = temp[l];
        }

    }

    public static void main(String[] args) {
        int[] ints = {5, 3, 4, 1, 2};
        mergeSort(ints,0,ints.length-1);
        System.out.println(Arrays.toString(ints));
    }
}
```

### 快速排序 

> 快速排序（英语：Quicksort），又称划分交换排序（partition-exchange sort），简称快排
> 一种排序算法，最早由东尼·霍尔提出。在平均状况下，排序 n 个项目要 O(nlogn) 次比较
> 。在最坏状况下则需要 O(n^2) 次比较，但这种状况并不常见
> 。事实上，快速排序 O(nlogn)通常明显比其他算法更快，因为它的内部循环（inner loop）可以在大部分的架构上很有效率地达成。

快速排序使用分治法(Divide and conquer) 策略来吧一个序列(List)分为两个子序列(sub-lists)  

步骤如下 :

1. 从数列中挑出一个元素，称为“基准”（pivot），
2. 重新排序数列，所有比基准值小的元素摆放在基准前面，所有比基准值大的元素摆在基准后面（相同的数可以到任何一边）。在这个分割结束之后，该基准就处于数列的中间位置。这个称为分割（partition）操作。
3. 递归地（recursively）把小于基准值元素的子数列和大于基准值元素的子数列排序。

> 递归到最底部时，数列的大小是零或一，也就是已经排序好了。这个算法一定会结束，因为在每次的迭代（iteration）中，它至少会把一个元素摆到它最后的位置去。

示例代码:

``` 
import java.util.Arrays;

public class QuickSort {
    public static void sort(int[] arr, int head, int tail) {
        if (head >= tail || arr == null || arr.length <= 1) {
            return;
        }
        //设置数组的起始位置 i 结束位置j 基准 pivot 为数组的中间
        int i = head, j = tail, pivot = arr[(head + tail) / 2];
        while (i <= j) {
            //当数组小于基准 循环结束后 相当于i所处的位置的值为大于基准的元素
            while (arr[i] < pivot) {
                ++i;
            }
            //当数组大于基准 循环结束后 相当于j所处的位置的值为小于于基准的元素
            while (arr[j] > pivot) {
                --j;
            }
            //如果i<j 那么则将交互i j对应位置的值
            if (i < j) {
                int t = arr[i];
                arr[i] = arr[j];
                arr[j] = t;
                //将指针继续移动
                ++i;
                --j;
            } else if (i == j) {
//如果i=j 那么说明本次排序已经结束 将i++ 如果这里不使用i++ 那么后面的sort(arr,i,tail)将改为arr(arr,i+1,tail)
                ++i;
            }
        }
        //继续将数组分割  
        sort(arr, head, j);
        sort(arr, i, tail);
    }

    public static void main(String[] args) {
        int[] ints = {5, 3, 4, 1, 2};
        sort(ints, 0, ints.length - 1);
        System.out.println(Arrays.toString(ints));
    }
}
```

### 搜索算法

> 线性搜索或顺序搜索是一种寻找某一特定值的搜索算法，指按一定的顺序检查数组中每一个元素，直到找到所要寻找的特定值为止。是最简单的一种搜索算法。

示例代码:

``` 
public class LinearSearch {
    public static void main(String[] args) {
        int[] ints = {5, 3, 4, 1, 2};
        System.out.println(search(ints, 4));
    }

    public static int search(int[] arr, int key) {
        //循环
        for (int i = 0; i < arr.length; i++) {
            //比较是否等于key
            if (arr[i] == key) {
                return arr[i];
            }
        }
        //找不到就返回-1
        return -1;
    }
}
```

### 二分查找  

> 在计算机科学中，二分搜索（英语：binary search），也称折半搜索（英语：half-interval search）、对数搜索（英语：logarithmic search）
> 是一种在有序数组中查找某一特定元素的搜索算法。搜索过程从数组的中间元素开始，如果中间元素正好是要查找的元素，则搜索过程结束
> 如果某一特定元素大于或者小于中间元素，则在数组大于或小于中间元素的那一半中查找，而且跟开始一样从中间元素开始比较
> 如果在某一步骤数组为空，则代表找不到。这种搜索算法每一次比较都使搜索范围缩小一半。

示例代码:

``` 
public class BinarySearch {
    public static int search(int[] arr, int key) {
        int low = 0;
        int high = arr.length - 1;
        while (low <= high) {
            int middle = (high + low) / 2;
            //如果相等 返回值
            if (key == arr[middle]) {
                return key;
            } else if (key < arr[middle]) {
                //如果key小于中间值，那么改变high，值可能在左边部（比较小的部分）
                high = middle - 1;
            }else {
                //如果key大于中间值，那么改变low，值可能在右边部（比较大的部分）
                low = middle + 1;
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        int[] ints = {1, 2, 3, 4, 5};
        System.out.println(search(ints, 4));
    }
}
```

