# 第一章 基础

[TOC]

## 1.1基础编程模型

### 基础命令行使用

| 命令  |                  参数                  |     作用     |
| :---: | :------------------------------------: | :----------: |
| javac |             .java文件名称              | 编译java程序 |
| java  | .class文件的名称（不需要扩展名）+ 参数 | 运行java程序 |
| more  |           任意格式文件的名称           | 打印文件内容 |

使用命令行编译一个名为Test.java的程序

```bash
javac Test.java
java Test
```

### 命令行与参数

​	Java类会包含一个静态方法main()，他有一个String数组类型的参数args[]。这个数组的内容就是我们输入的命令行参数，操作系统会将它传递给Java。

使用命令行编译一个有参数的名为Test.java的程序

```bash
//命令行
 javac Test.java
 java Test 5 100 200
```

<img src="..\image\QQ图片20220705151918.png" alt="命令行解析" style="zoom:67%;" />

````java
public class Test {

    public static void main(String[] args) {
        int N = Integer.parseInt(args[0]);//5
        double lo = Double.parseDouble(args[1]);//100
        double hi = Double.parseDouble(args[2]);//200
        for(int i=0;i<N;i++){
            double x = lo+Math.random()*(hi-lo);
            System.out.println(x);
        }
    }
}
````

### 标准输入

标准输入流最重要的特点是这些值会在你的程序读取他们之后消失。只要程序读取了一个值，他就不能回退并再次读取它。

```java
import Tool.StdIn;
public class Average {
    public static void main(String[] args) {
        double sum = 0.0;
        int cnt = 0;
        while(!StdIn.isEmpty()){
            sum += StdIn.readDouble();
            cnt++;
        }
        double avg = sum/cnt;
        System.out.println("Average is "+avg);
    }
}
```

直接使用idea运行Average

<img src="../image/QQ图片20220812174548.png"  >

在cmd用命令行运行Average

```bash
//用例：计算标准输入的数字的平均值
java Average //运行java程序
3             //键盘输入后，回车
4
5
6
7
<ctrl-z>      //键盘上按ctrl+z
The average is 5.0  // 
```

在idea的Terminal中运行java程序

<img src="..\image\QQ图片20220706104948.png" alt="命令行解析"  />

<img src="..\image\QQ图片20220812174214.png" alt="命令行解析"  />

### 重定向与管道

将标准输出**重定向**到一个文件中:

```bash
java RandomSeq 1000 100 200 > data.txt
```



可以使用**重定向**标准输入以使StdIn从文件中（而不是终端应用程序中）读取数据：

```shell
java Average < data.txt

```

将一个程序的输出重定向为另一个程序的输入叫做管道：

```shell
java RandomSeq 1000 100 200 | java Average
```

### 1.2数据抽象

* API

我们使用应用程序编程接口（API）来说明抽象数据类型的行为。它将列出所有构造函数和实例方法并简要描述他们的功能。

### 1.3背包队列和栈

* 对集合中的对象的表示方式将直接影响各种操作的效率。
* 泛型和迭代
* 链表

#### 1.3.1  泛型

在每份API中，类名后的<Item>记号将Item定义为一个类型参数，表示的是用例将会使用的具体数据类型。在创建栈时，用例会提供一种具体的数据类型，我们可以将Item替换为任意引用数据类型。

#### 1.3.2 API

泛型可迭代的基础集合数据结构类型的API

| 背包：一种不支持从中删除元素的集合数据类型，迭代的顺序不确定、且与用例无关。 |
| ------------------------------------------------------------ |
| pulibc class Bag<Item> implements Iterable<Item>             |
| Bag()                              创建一个空背包            |
| void         add(Item item)               添加一个元素       |
| boolean   isEmpty()                        背包是否为空      |
| int             size()                               背包中的元素数量 |

| 先进先出（FIFO）队列：一种基于先进先出策略的集合类型。保存集合元素相对位置，使他们入队顺序与出对顺序相同。 |
| ------------------------------------------------------------ |
| public class Queue<Item> implements Iterable<Item>           |
| Queue()                              创建空队列              |
| void          enqueue(Item item)           添加一个元素      |
| Item          dequeue()                           删除最早添加的元素 |
| boolean    isEmpty()                            队列是否为空 |
| int              size()                                   队列中的元素数量 |

| 下压栈：一种基于后进先出策略的集合类型。                     |
| ------------------------------------------------------------ |
| public class Stack<Item> implements Iterable<Item>           |
| Stack()                             创建一个空栈             |
| void             push(Item item)               添加一个元素  |
| Item             pop()                                 删除最近添加的元素 |
| boolean       isEmpty()                          栈是否为空  |
| int                 size()                                 栈中的元素数量 |

#### 1.3.3 自动装箱

类型参数必须被实例化为**引用类型**，因此Java有一种特殊机制来使泛型代码能够处理原始数据类型。在处理赋值语句、方法的参数和算术或逻辑表达式时，Java对自动在引用类型和对应的原始数据类型之间进行转换。

````java
Stack<Integer> stack = new Stack<Integer>();
stack.push(12);               //自动装箱（int=>Integer）
int i = stack.pop();          //自动拆箱（Integer=>int）
````

* 双栈算术表达式求值算法

````java
import Tool.StdIn;
import java.util.Stack;
public class Evaluate {
    public static void main(String[] args) {
        Stack<String> ops = new Stack<String>();
        Stack<Double> vals = new Stack<Double>();
        while(!StdIn.isEmpty()){          //读取字符
            String s = StdIn.readString();//如果是运算符则压入运算符栈
            if(s.equals("("))             //如果遇到“(”,则忽略
                ;
            else if(s.equals("+"))
                ops.push(s);
            else if(s.equals("-"))
                ops.push(s);
            else if(s.equals("*"))
                ops.push(s);
            else if(s.equals("/"))
                ops.push(s);
            else if(s.equals("sqrt"))
                ops.push(s);
            else if(s.equals(")")){        //如果字符为“）”，弹出运算符和操作数，计算结果并压入栈
                String op = ops.pop();
                double v = vals.pop();
                if(op.equals("+"))
                    v = vals.pop() + v;
                else if(op.equals("-"))
                    v = vals.pop() - v;
                else if(op.equals("*"))
                    v = vals.pop() * v;
                else if(op.equals("/"))
                    v = vals.pop() / v;
                else if(op.equals("sqrt"))
                    v = Math.sqrt(v);
                vals.push(v);
            }
            //如果不是运算符和括号，则压入操作数栈
            else vals.push(Double.parseDouble(s));
        }
        System.out.println(vals.pop());
    }
}
````

#### 1.3.4 支持泛型和迭代的栈、队列、背包代码实现

| 数据结构 | 优点                           | 缺点                           |
| -------- | ------------------------------ | ------------------------------ |
| 数组     | 通过索引可以直接访问任何元素   | 在初始化时就需要知道元素的数量 |
| 链表     | 使用的空间大小与元素数量成正比 | 需要通过引用访问任意元素       |

* 基于数组的栈的实现(泛型参数、可调节大小、可逆序遍历)

```java
import java.util.Iterator;

public class ResizingArrayStack<Item> implements Iterable<Item> {
    private Item[] a = (Item[]) new Object[1];
    private int N = 0;
    public boolean isEmpty(){
        return N==0;
    }
    public int size(){
        return N;
    }
    private void resize(int max){
        Item[] temp = (Item[]) new Object[max];
        for(int i=0;i<N;i++){
            temp[i] = a[i];
        }
        a = temp;
    }
    public void push(Item item){
        if(N==a.length){
            resize(a.length*2);
        }
        a[N++] = item;
    }
    public Item pop(){
        if(N>0) {
            Item item = a[--N];
            a[N] = null;
            if(N==a.length/4)
                resize(a.length/2);
            return item;
        }
        else
            return null;
    }
    public Iterator<Item> iterator(){
        return new ReverseArrayIterator();
    }
    public class ReverseArrayIterator implements Iterator<Item>{
        private int i = N;
        public boolean hasNext(){
            return  i>0;
        }
        public Item next(){
            return a[--i];
        }
        public void remove(){}
    }

    public static void main(String[] args) {
        ResizingArrayStack<String> s;
        s = new ResizingArrayStack<String>();
        s.push("hello");
        s.push("My");
        s.push("name");
        //s.pop();
        //s.pop();
        s.push("is");
        //s.pop();
        s.push("Lumos");
        System.out.println(s.size());
        for(String a:s){
            System.out.println(a);
        }
    }
}

```

**链表定义** 链表是一种递归的数据结构，他或者为空(null)，或者是含有泛型元素的结点和指向另一条链表的引用。

* 基于链表的栈的实现

```java
public class LinkedlistStack<Item>  {
    private Node first;
    private int N;
    private class Node{
        Item item;
        Node next;
    }
    public boolean isEmpty(){
        return first == null;
    }
    public int size(){
        return  N;
    }
    public void push(Item item){
        Node oldFirst = first;
        first = new Node();
        first.item = item;
        first.next = oldFirst;
        N++;
    }
    public Item pop(){
        Item item = first.item;
        first = first.next;
        N--;
        return item;
    }
    public void write(Node first){
        for(Node d = first;d!=null;d=d.next){
            System.out.println(d.item);
        }
    }
    public static void main(String[] args) {
        LinkedlistStack<String> s;
        s = new LinkedlistStack<>();
        s.push("hello");
        s.push("My");
        s.push("name");
        //s.pop();
        //s.pop();
        s.push("is");
       //s.pop();
        s.push("Lumos");
        System.out.println(s.size());
        s.write(s.first);
//        for(String a:s){
//           System.out.println(a);
//        }
    }
}

```

* 基于链表的队列实现

```java
import Tool.StdIn;
import java.util.Queue;

public class LinkedlistQueue<Item>{
    private Node first;
    private Node last;
    private int N;
    private class Node{
        Item item;
        Node next;
    }
    public boolean isEmpty(){
        return first == null;
    }
    public int size(){
        return N;
    }
    public void enqueue(Item item){
        Node oldLast = last;
        last = new Node();
        last.item = item;
        if(isEmpty())
            first = last;
        else
            oldLast.next = last;
        N++;
    }
    public Item dequeue(){
        if(!isEmpty()){
            Item item = first.item;
            first = first.next;
            N--;
            if(first==null){
                last = null;
            }
            return item;
        }
        else
            return null;
    }

    public static void main(String[] args) {
        LinkedlistQueue<String> q = new LinkedlistQueue<String>();
        while(!StdIn.isEmpty()){
            String item = StdIn.readString();
            if(!item.equals("-")){
                q.enqueue(item);
            }
            else if(!q.isEmpty())
                System.out.print(q.dequeue()+" ");
        }
        System.out.println("("+q.size()+" left on queue)");
    }
}

```

* 支持泛型和迭代基于链表的背包实现

````java
import java.util.Iterator;

public class LinkedlistBag<Item> implements Iterable<Item> {
    private Node first;
    private class Node{
        Item item;
        Node next;
    }
    public void add(Item item){
        Node oldFirest = first;
        first = new Node();
        first.item = item;
        first.next = oldFirest;
    }
    public Iterator<Item> iterator(){
        return new LinkedlistIterator();
    }
    private class LinkedlistIterator implements Iterator<Item>{
        private Node current = first;
        @Override
        public boolean hasNext() {
            return current!=null;
        }

        @Override
        public Item next() {
            Item item = current.item;
            current = current.next;
            return item;
        }
    }

    public static void main(String[] args) {
        LinkedlistBag<String> bag = new LinkedlistBag<String>();
        bag.add("My");
        bag.add("My2");
        bag.add("My3");
        bag.add("My4");
        bag.add("My5");
        bag.add("My6");
        bag.add("My7");
        for(String b:bag){
            System.out.print(b+" ");
        }
    }
}

````

------



## 补充：

### 1.初次将文件夹上传到github中

**需求：将note文件夹和image文件夹上传到github上**

1. 在github上创建一个名为StudyNote仓库,并复制仓库地址

<img src="../image/QQ图片20220713114212.png">

2. 本地创建一个Study的空文件夹

<img src="../image/QQ图片20220713115200.png" style="zoom:80%;" />

3. 系统弹出git bash框，输入代码

```bash
$ git clone https://github.com/xttlalala/StudyNote.git
```

<img src="../image/QQ图片20220713115800.png" />

在github上创建的库以文件夹的形式（StudyNote）落在Study目录中

4. 将note文件夹和image文件夹复制到StudyNote中

<img src="../image/QQ图片20220713120138.png" />

5. 回到git bash界面，输入以下代码：

```bash
$ cd StudyNote
$ git init
$ git add .
$ git commit -m "first-commit"
$ git push
```

<img src="../image/QQ图片20220713121531.png" alt="QQ图片20220713121531" style="zoom:150%;" />

本地文件夹即可上传到github仓库中。

### 2.修改后再次提交

1. 我对readme.md文件进行了修改

<img src="../image/QQ图片20220713121814.png" alt="QQ图片20220713121814" style="zoom:80%;" />

2. 也可以直接在StudyNote文件夹中进入git bash

<img src="../image/QQ图片20220713122214.png" style="zoom:80%;" />

3. 在git bash中输入命令

```bash
$ git add .
$ git commit -m "update"
$ git push
```



<img src="../image/QQ图片20220713122417.png" style="zoom:70%;" />

4. 成功同步到github仓库

<img src="../image/QQ图片20220713122715.png" style="zoom:60%;" />
