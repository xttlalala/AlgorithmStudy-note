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

> 性能:它的运行时间达不到平方级别。



## 2.2归并算法

​    递归实现的归并排序是算法设计中**分治思想**典型应用。         

​    归并：将两个有序的数组归并成一个更大的数组。

```java
//自顶向下的归并排序
public class Merge {
    private static Comparable[] aux;//归并所需的辅助数组
    public static void sort(Comparable[] a){
        aux = new Comparable[a.length];
        sort(a,0,a.length-1);
    }
    public static void sort(Comparable[] a,int lo,int hi){
        if(hi<=lo)
            return;;
        int mid = lo+(hi-lo)/2;
        sort(a,lo,mid);
        sort(a,mid+1,hi);
        merge(a, lo, mid, hi);
    }
    //原地归并的实现
    public static void merge(Comparable[] a,int lo,int mid,int hi){
        int i = lo;
        int j = mid+1;
        for(int k=lo;k<=hi;k++){
            aux[k] = a[k];
        }
        for(int k=lo;k<=hi;k++){
            if(i>mid) a[k] = aux[j++];
            else if (j>hi) a[k] = aux[i++];
            else if(less(aux[j],aux[i]))  a[k]=aux[j++];
            else {a[k]=aux[i++];};
        }
    }
    public static void main(String[] args) {
        String[] a = StdIn.readAllStrings();
        sort(a);
        assert isSorted(a);
        show(a);
    }
}                                                              
```

> 性能：
>
>   优点：任意长度N的数组排序所需时间和NlgN成正比。
>
>    缺点：需要和N成正比的额外空间。（将涉及所有元素复制到一个辅助数组中，再把归并的结果放回原数组中）

> 命题F。对于数组长度为N的任意数组，自顶向下的归并排序需要1/2NlgN至NlgN次比较。
>
> 命题G。对于数组长度为N的任意数组，自顶向下的归并排序最多需要访问数组6NlgN次。

```java
//自底向下的归并排序
public class MergeBU {
    private static Comparable[] aux;
    public static void sort(Comparable[] a){
        int N = a.length;
        aux = new Comparable[N];
        for(int sz=1;sz<N;sz=sz*2){
            for(int lo=0;lo<N-sz;lo+=sz*2){
                merge(a,lo ,lo+sz-1 , Math.min(lo+2*sz-1, N-1));
            }
        }
    }
    public static void merge(Comparable[] a,int lo,int mid,int hi){
        int i = lo;
        int j = mid+1;
        for(int k=lo;k<=hi;k++){
            aux[k] = a[k];
        }
        for(int k=lo;k<=hi;k++){
            if(i>mid) a[k] = aux[j++];
            else if (j>hi) a[k] = aux[i++];
            else if(less(aux[j],aux[i]))  a[k]=aux[j++];
            else {a[k]=aux[i++];};
        }
    }
    public static void main(String[] args) {
        String[] a = StdIn.readAllStrings();
        //sort(a);
        assert isSorted(a);
        show(a);
    }
}
```

>命题H。对于长度为N的任意数组，自底向上的归并排序需要1/2NlgN至NlgN次比较，最多访问数组6NlgN次。

## 2.3快速排序

快速排序是一个分治的排序算法。它将一个数组分成两个子数组，将两部分独立的排序，**切分(partition)**的位置取决于数组的内容。

该方法的关键在于切分，这个过程使数组满足三个条件：

* 对于某个j，a[j]的位置已经排定；
* a[lo]到a[j-1]中的所有元素都不大于a[j];
* a[j+1]到a[hi]中的所有元素都不小于a[j];

```java
public class Quick {
    public static void sort(Comparable[] a){
        StdRandom.shuffle(a);//随机 
        sort(a, 0, a.length-1);
    }
    public static void sort(Comparable[] a,int lo,int hi){
        if(hi<=lo)
            return;
        int j = partition(a, lo, hi);
        sort(a,lo,j-1);
        sort(a,j+1,hi);
    }
    //快速排序的划分
    private static int partition(Comparable[] a,int lo,int hi){
        int i = lo;
        int j = hi+1;
        Comparable v = a[lo];
        while(true){
            while(less(a[++i],v)) if(i==hi) break;
            while(less(v,a[--j])) if(j==lo) break;
            if(i>=j) break;
            exch(a, i, j);
        }
        exch(a, lo, j);
        return j;
    }
    public static void main(String[] args) {
        String[] a = StdIn.readAllStrings();
        sort(a);
        assert isSorted(a);
        show(a);

    }
}
```

> 性能：
>
> 快速排序引人注目的特点包括它是原地排序，且将长度为N的数组排序**所需的时间和NlgN成正比**。
>
> 潜在的缺点：在切分不平衡时这个程序可能会极其低效。

> 命题L。快速排序最多需要约N²/2次比较，但随机打乱数组可以预防这种情况。
