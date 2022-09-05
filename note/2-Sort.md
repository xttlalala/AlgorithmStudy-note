# 第二章 排序

[TOC]

## 2.1初级排序算法

### 2.1.1游戏规则

* 排序算法类的模版

```java
import Tool.StdIn;

public class Example {
    public static void sort(Comparable[] a){

    }
    private static boolean less(Comparable v,Comparable w){
        return v.compareTo(w)<0;
    }
    private static void exch(Comparable[] a,int i,int j){
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
    private static void show(Comparable[] a){
        for(Comparable c:a){
            System.out.print(c+" ");
        }
        System.out.println();
    }
    private static boolean isSorted(Comparable[] a){
        for(int i=1;i<a.length;i++){
            if(less(a[i],a[i-1])){
                return false;
            }
        }
        return true;
    }

    public static void main(String[] args) {
        String[] a = StdIn.readAllStrings();
        sort(a);
        assert isSorted(a);
        show(a);

    }
}

```

### 2.1.2选择排序

 **选择排序**： 首先，找到数组中最小的元素，其次将它和数组中第一个元素交换位置。再次，在剩下的元素中找到最小的元素，将它与数组的第二个元素交换位置。如此往复，直到将整个数组排序。

 这种方法叫做选择排序，因为它在不断地选择剩余元素中的最小者。

```java
public static void sort(Comparable[] a){
        int N = a.length;
        for(int i=0;i<N;i++){
            int min = i;
            for(int j = i+1;j<N;j++){
                if(less(a[j],a[min])){
                    min = j;
                }
            }
            exch(a, i, min);
        }
    }
```

性能：对于长度为N的数组，选择排序需要大约N²/2次比较和N次交换。



### 2.1.3插入排序

**插入排序**：一个元素一个元素的来，将每一个元素插入到其他已经有序的元素中的适当位置。

插入排序不会访问索引右侧的元素，另外，插入排序不会移动比插入的元素更小的元素。

```java
public static void sort(Comparable[] a){
        int N = a.length;
        for(int i=1;i<N;i++){
            for(int j=i;j>0&&less(a[j],a[j-1]);j--){
                exch(a,j,j-1);
            }
        }
    }
```

​     **倒置**指的是数组中两个顺序颠倒的元素。比如EXAMPLE中有11对倒置：E-A,X-A,X-M,X-P,X-L,X-E,M-L,M-E,P-L,P-E,L-E。如果数组中倒置的数量小于数组大小的某个倍数，那么我们说这个数组是**部分有序**的。

部分有序的数组：

* 数组中每个元素距离他的最终位置都不远
* 一个有序的大数组接一个小数组
* 数组中只有几个元素位置不正确

**插入排序对于这样部分有序的数组很有效。**

> 命题C。插图排序需要的交换操作和数组中倒置的数量相同；需要的比较次数大于等于倒置的数量，小于等于倒置的数量加上数组的大小-1。



性能：平均情况下需要 ~N²/4次比较  ，~N²/4次交换；

​           最坏情况下需要 ~N²/2次比较  ，~N²/2次交换；

​           最好情况下需要 N-1次比较  ，0次交换；



### 2.1.4希尔排序

​      希尔排序是为了加快速度而改进了插入排序，交换不相邻的元素以对数组的局部进行排序，并最终使用插入排序将局部有序的数组进行排序。

希尔排序的思想是使数组中任意间隔为h的元素都是有序的。

> 递增序列：好的递增序列决定着算法的性能。
>
> ​					此处使用1/2(3^k-1)的递增序列：1,4,13,40,121,364,1093....

​       如果我们在插入排序中加入一个外循环，以使间隔h按照“递增序列”递减，我们将得到一个基础的希尔排序。

```java
public static void sort(Comparable[] a){
        int N = a.length;
        int h = 1;
        while(h<N/3){
            h = 3*h+1;
        }
        while(h>=1){
            for(int i=h;i<N;i++){
                for(int j=i;j>=h&&less(a[j],a[j-h]);j=j-h){
                    exch(a, j, j-h);
                }
            }
            h = h/3;
        }
    }
```

性能:它的运行时间达不到平方级别。

## 2.2归并算法

​    递归实现的归并排序是算法设计中分治思想典型应用。                                                                                                
