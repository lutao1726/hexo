---
title: Java常用算法汇总
date:  2018/5/16 17:29
categories: 面试
tags: [算法 , Java , Java基础]

---
<!-- 展示图片 -->

#### 排序
###### 冒泡排序
>冒泡排序（Bubble Sort）也是一种简单直观的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢"浮"到数列的顶端。

1. 算法步骤
比较相邻的元素。如果第一个比第二个大，就交换他们两个。
对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。    
针对所有的元素重复以上的步骤，除了最后一个。
持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。
2. 动图演示
![Mou_icon](http://blog.lutao1726.top/bubbleSort.gif)

```java
public class BubbleSort{
    public static void main(String[] args){
       int[] arr = {88,44,66,22,99,33,55,11,77,10};
       arr = new BubbleSort().bubbleSort(arr);
       System.out.println("排序结果：");
       for (int i: arr) {
          System.out.print(i+"    ");
       }
    }
    public int[] bubbleSort(int[] arr){
        if(arr.length<2){
            return arr;
        }
        int length = arr.length;
        for(int i =0 ;i<arr.length;i++){
            for(int j = i+1;j<arr.length;j++ ){
                if(arr[i]>arr[j]){
                    swap(arr,i,j);
                }
            }
        }
        return arr;
    }
   
    public void swap(int[] arr, int i,int j){
        arr[i]=arr[i]^arr[j];
        arr[j]=arr[i]^arr[j];
        arr[i]=arr[i]^arr[j];
    }
    
}
```
###### 快速排序
>快速排序是由东尼·霍尔所发展的一种排序算法。在平均状况下，排序 n 个项目要 Ο(nlogn) 次比较。在最坏状况下则需要 Ο(n2) 次比较，但这种状况并不常见。事实上，快速排序通常明显比其他 Ο(nlogn) 算法更快，因为它的内部循环（inner loop）可以在大部分的架构上很有效率地被实现出来。  
 快速排序使用分治法（Divide and conquer）策略来把一个串行（list）分为两个子串行（sub-lists）。
 快速排序又是一种分而治之思想在排序算法上的典型应用。本质上来看，快速排序应该算是在冒泡排序基础上的递归分治法。
 快速排序的名字起的是简单粗暴，因为一听到这个名字你就知道它存在的意义，就是快，而且效率高！它是处理大数据最快的排序算法之一了。

1. 算法步骤     
从数列中挑出一个元素，称为 "基准"（pivot）;              
重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；         
递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序；       
2. 动图演示
![Mou_icon](http://blog.lutao1726.top/quickSort.gif)

```java
public class QuickSort{
    public static void main(String[] args){
      int[] arr = {88,44,66,22,99,33,55,11,77,10};
      arr = new QuickSort().quickSort(arr,0,arr.length-1);
      System.out.println("排序结果：");
      for (int i: arr) {
          System.out.print(i+"    ");
      }
    }
    public int[] quickSort(int[] arr,int left,int right){
        if(left>=right){
            return arr;
        }
        int index = getIndex(arr,left,right);
        quickSort(arr,left,index-1);
        quickSort(arr,index+1,right);
        return arr;
    }
    public int getIndex(int[] arr ,int left,int right){
        int temp = arr[left];
        int i = left;
        int j = right;
        while(i<j){
            while (arr[j]>=temp&&i<j){
                j--;
            }
            while (arr[i]<=temp&&i<j){
                i++;
            }
            if(i<j){
                swap(arr,i,j);
            }
        }
        swap(arr,left,i);
        return i;
    }
    public void swap(int[] arr, int i,int j){
        arr[i]=arr[i]^arr[j];
        arr[j]=arr[i]^arr[j];
        arr[i]=arr[i]^arr[j];
    }
    
}
```

###### 堆排序
>堆排序（Heapsort）是指利用堆这种数据结构所设计的一种排序算法。堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。堆排序可以说是一种利用堆的概念来排序的选择排序。分为两种方法：    
 大顶堆：每个节点的值都大于或等于其子节点的值，在堆排序算法中用于升序排列；  
 小顶堆：每个节点的值都小于或等于其子节点的值，在堆排序算法中用于降序排列；  
 堆排序的平均时间复杂度为 Ο(nlogn)。

1. 算法步骤
创建一个堆 H[0……n-1]；

把堆首（最大值）和堆尾互换；

把堆的尺寸缩小 1，并调用 shift_down(0)，目的是把新的数组顶端数据调整到相应位置；

重复步骤 2，直到堆的尺寸为 1。
2. 动图演示
![Mou_icon](http://blog.lutao1726.top/heapSort.gif)

```java
public class HeapSort{
    public static void main(String[] args){
      int[] arr = {88,44,66,22,99,33,55,11,77,10};
      new HeapSort().heapSort(arr);
      System.out.println("排序结果：");
      for (int i: arr) {
          System.out.print(i+"    ");
      }
    }
    public void heapSort(int[] arr){
        int n = arr.length;
        for(int i = n/2 -1 ;i>=0 ;i--){
            heapify(arr,n,i);
        }
        for(int i = n -1 ;i>0 ; i--){
            swap(arr,i,0);
            heapify(arr,i,0);
        }
    }
    public void heapify(int[] arr,int n,int i){
        int largest = i;
        int left = i*2 +1;
        int right = i*2 +2;
        if(left<n&&arr[left]>arr[largest]){
            largest = left;
        }
        if(right<n&&arr[right]>arr[largest]){
            largest = right;
        }
        if(largest!=i){
            swap(arr,largest,i);
            heapify(arr,n,largest);
        }
    }
    public void swap(int[] arr, int i,int j){
        arr[i]=arr[i]^arr[j];
        arr[j]=arr[i]^arr[j];
        arr[i]=arr[i]^arr[j];
    }
    
}
```

#### 查找
###### 二分查找
> 二分查找也称折半查找（Binary Search），它是一种效率较高的查找方法。但是，折半查找要求线性表必须采用顺序存储结构，而且表中元素按关键字有序排列。

```java
public class BinarySearch{
  public static int binarySearch(Integer[] srcArray, int des) {
      //定义初始最小、最大索引
      int start = 0;
      int end = srcArray.length - 1;
      //确保不会出现重复查找，越界
      while (start <= end) {
          //计算出中间索引值
          int middle = (end + start)>>>1 ;//防止溢出
          if (des == srcArray[middle]) {
              return middle;
          //判断下限
          } else if (des < srcArray[middle]) {
              end = middle - 1;
          //判断上限
          } else {
              start = middle + 1;
          }
      }
      //若没有，则返回-1
      return -1;
  }
}
```