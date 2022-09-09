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

* 运行时间

​		我们要评估算法的**性能**。首先，要计算各个排序算法在不同的随机输入下的基本操作(比较、交换、读写数组)的次数。然后，我们用这些数据来估计算法的相对性能。

* 额外的内存使用

​		排序算法的额外内存开销和运行时间是同等重要的。排序算法可以分为两类：除了函数调用所需的栈和固定数目的实例变量之外无需额外内存的原地排序算法，以及需要额外内存空间来存储另一份数组副本的其他排序算法。

* 数据类型

​		上面的排序模版适用于任何实现了Comparable接口的数据类型。

​		Java中封装数字类型的Integer和Double，以及String和其他许多高级数据类型(File、URL)都实现了Comparable接口。

​		在创建自己的数据类型时，我们只要实现Comparable接口就能保证用例代码将其排序。于是，我们需要实现一个 **compareTo()** 方法来定义目标类型对象的自然次序。

```java
public class Date implements Comparable<Date> {
    private final int day;
    private final int month;
    private final int year;

    public Date(int day, int month, int year) {
        this.day = day;
        this.month = month;
        this.year = year;
    }
    @Override
    public int compareTo(Date that) {
        if(this.year > that.year) return +1;
        if(this.year < that.year) return -1;
        if(this.month > that.month) return +1;
        if(this.month < that.month) return -1;
        if(this.day > that.day) return +1;
        if(this.day < that.day) return -1;
        return 0;
    }
    public String toString(){
        return month+"/"+day+"/"+year;
    }
}
```

​	**总之，compareTo()实现了我们的主键抽象——他给出了Comparable接口的任意数据类型的对象的大小顺序的定义。**

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

​		**倒置**指的是数组中两个顺序颠倒的元素。比如EXAMPLE中有11对倒置：E-A,X-A,X-M,X-P,X-L,X-E,M-L,M-E,P-L,P-E,L-E。如果数组中倒置的数量小于数组大小的某个倍数，那么我们说这个数组是**部分有序**的。

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

​		希尔排序是基于插入排序的更快速的排序算法。交换不相邻的元素以对数组的局部进行排序（插入排序对大规模乱序数组排序很慢，因为他只会交换相邻的元素，因此元素只能一点一点的从数组的一端到另一端），并最终使用插入排序将局部有序的数组进行排序。

​		希尔排序的思想是使数组中任意间隔为h的元素都是有序的。

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



## 2.4优先队列

优先队列是一个抽象数据类型，它表示了一组值和对这些值的操作。

### 2.4.1API

泛型优先队列的API

| public class MaxPQ<Key extends Comparable<Key>>              |
| ------------------------------------------------------------ |
| MaxPQ()                                         创建一个优先队列 |
| MaxPQ(int max)                            创建一个初始容量为max的优先队列 |
| MaxPQ(Key[] a)                             用a[ ]中的元素创建一个优先队列 |
| void    insert(Key v)                                  向优先队列中插入一个元素 |
| Key     max()                                             返回最大元素 |
| Key     delMax()                                        删除并返回最大元素 |
| boolean isEmpty()                                    返回队列是否为空 |
| int       size()                                             返回优先队列中的元素个数 |

### 2.4.2堆的定义

> 定义:当一棵二叉树的每个结点都大于等于它的两个子结点时，它被称为堆有序。

**二叉堆表示法**

​		完全二叉树只用数组，而不需用指针就可以表示。

​		具体方法就是将二叉树的结点按照层级顺序放入数组中，根节点在位置1，它的子节点在位置2和3，而子结点的子结点则分别在位置4、5、6和7，以此类推。

> **定义。** **二叉堆** 是一组能够用堆有序的完全二叉树排序的元素，并在数组中按照层级储存（不使用数组的第一个位置）。

> **命题P。** 一课大小为N的完全二叉树的高度为lgN(向下取整)。

### 2.4.3堆的算法

​		使用有序或无序数组的优先队列的初级实现总是需要线性时间来完成其中的操作，而基于堆的实现能保证我们在对数时间内完成它们。

* 面向最大元素的优先队列

```java
public class MaxPQ<Key extends Comparable<Key>>{
    private Key[] pq;
    private int N = 0;
    public MaxPQ(int maxN){
        pq = (Key[]) new Comparable[maxN+1];
    }
    public boolean isEmpty(){
        return N==0;
    }
    public int size(){
        return N;
    }
    public void insert(Key v){
        pq[++N] = v;//不使用pq[0]
        swim(N);
    }
    public Key delMax(){
        Key max = pq[1];
        exch(1,N);
        pq[N] = null;
        N--;
        sink(1);
        return max;
    }
    private void swim(int k){
        while(k>1&&less(k/2,k)){
            exch(k/2,k);
            k = k/2;
        }
    }
    private void sink(int k){
        while(2*k<=N){
            int j = 2*k;
            if(j<N&&less(j,j+1)){
                j++;
            }
            if(!less(k,j)) break;;
            exch(k,j);
            k=j;
        }
    }
    private boolean less(int i,int j){
        return pq[i].compareTo(pq[j])<0;
    }
    private void exch(int i,int j){
        Key t = pq[i];
        pq[i] = pq[j];
        pq[j] = t;
    }

    public static void main(String[] args) {
        int M = Integer.parseInt(args[0]);
        MaxPQ<Transaction> pq = new MaxPQ<Transaction>(M+1);
        while (StdIn.hasNextLine()){
            //为下一行输入创建一个元素并放入优先队列中
            Transaction t =new Transaction(StdIn.readLine());
            pq.insert(t);//插入后进行有序化
            if(pq.size()>M){//有序化后再弹出最大元素（所以应该建立M+2大小的有限序列)
                pq.delMax();
            }
        }
        Stack<Transaction> stack = new Stack<Transaction>();
        while (!pq.isEmpty()){
            stack.push(pq.delMax());
        }
        for(Transaction t:stack)
            System.out.println(t);
    }
}

```

* 、面向最小元素的优先队列

```java
public class MinPQ<Key extends Comparable<Key>> {
    private Key[] pq;
    private  int N = 0;
    public MinPQ(int maxN){
        pq = (Key[]) new Comparable[maxN+1];
    }
    public Key delMin(){
        Key min = pq[1];
        exch(1,N);
        pq[N] = null;
        N--;
        sink(1);
        return min;
    }
    private void swim(int k){
        while(k>1&&less(k,k/2)){
            exch(k,k/2);
            k = k/2;
        }
    }
    private void sink(int k){
        while(2*k<=N){
            int j = 2*k;
            if(j<N&&less(j+1,j)){
                j++;
            }
            if(!less(j,k))break;
            exch(k,j);
            k = j;
        }
    }
}

```

> 命题Q。对于一个含有N个元素的基于堆的有限序列，**插入元素** 操作只需不超过 ( lgN+1 )次比较，**删除最大元素** 的操作需要不超过 2lgN 次比较。



关联索引的泛型优先队列的API

| public class IndexMinPQ<Item extends Comparable<Item>>       |
| ------------------------------------------------------------ |
| IndexMinPQ(int maxN)             创建一个最大容量为maxN的优先队列，索引的取值范围为0至maxN-1 |
| void    insert(int k,Item item)               插入一个元素，将它和索引k相关联 |
| void    change(int k,Item item)             将索引为k的元素设为item |
| boolean contains(int k)                        是否存在索引为k的元素 |
| void     delete(int k)                               删去索引k及其相关联的元素 |
| Item     min()                                          返回最小元素 |
| int        minIndex()                                 返回最小元素的索引 |
| int        delMin()                                     删除最小元素并返回它的索引 |
| boolean isEmpty()                                 优先队列是否为空 |
| int         size()                                         优先队列中的元素个数 |

### 2.4.5堆排序

#### 2.4.5.1堆的构造

​		在排序的第一阶段，我们的目标是产生一个堆有序的结果。使用sink()我们只需要扫描数组一半的元素，因为我们可以跳过大小为1的子堆。

> **命题R。** 用下沉操作由N个元素构造堆只需少于2N次比较以及少于N次交换。

#### 2.4.5.2下沉排序

​		堆排序的主要工作都是在第二阶段完成的，每次将堆中的最大元素删除(将其与最末元素交换，再N--)。 

​		这个过程和选择排序有些类似，但所需的比较要少得多，因为堆提供了一种从未排序的部分找到最大元素的有效方法。

> **命题S。** 将N个元素排序，堆排序只需少于（2NlgN+2N）次比较，以及一般的交换。
>
> 2N项来自堆的构造；2NlgN项来自于每次下沉操作最大可能需要2lgN的比较

```java
public class Heapsort {
    public static void sort(Comparable[] a){
        int N = a.length-1;
        System.out.println(N);
        for(int k = N/2;k>=1;k--){
            sink(a,k,N);
        }
        while(N>1){
            exch(a,N--,1);
            sink(a,1,N);
        }
    }
    private static void sink(Comparable[] a,int k,int N){
        while(2*k<=N){
            int j = 2*k;
            if(j<N&&less(a,j,j+1)){
                j++;
            }
            if(!less(a,k,j)) break;;
            exch(a,k,j);
            k=j;
        }
    }
    private static boolean less(Comparable[] a,int i,int j){
        return a[i].compareTo(a[j])<0;
    }
    private static void exch(Comparable[] a,int i,int j){
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
    private static void show(Comparable[] a){
        for(int i=1;i<a.length;i++)
            System.out.print(a[i]+" ");
        System.out.println();
    }
    public static void main(String[] args) {
        String[] a = StdIn.readAllStrings();
        String[] b = new String[a.length+1];
        b[0]=null;
        for(int i=1;i<b.length;i++)
            b[i]=a[i-1];
        sort(b);
        show(b);
    }
}

```

​		**堆排序是我们所知的唯一能够同时最优地利用空间和时间的方法——在最坏情况下它也能保证使用~2NlgN次比较和恒定的额外空间** 。

​		它能在**插入操作** 和**删除最大元素操作** 混合的动态场景中保证对数级别的运行时间。
