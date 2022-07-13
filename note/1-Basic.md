

# 第一章 基础

## 1.1基础编程模型

### 基础命令行使用

| 命令  |                  参数                  |     作用     |
| :---: | :------------------------------------: | :----------: |
| javac |             .java文件名称              | 编译java程序 |
| java  | .class文件的名称（不需要扩展名）+ 参数 | 运行java程序 |
| more  |           任意格式文件的名称           | 打印文件内容 |

使用命令行编译一个名为Test.java的程序

```
% javac Test.java
% java Test
```

### 命令行与参数

​	Java类会包含一个静态方法main()，他有一个String数组类型的参数args[]。这个数组的内容就是我们输入的命令行参数，操作系统会将它传递给Java。

使用命令行编译一个有参数的名为Test.java的程序

```
//命令行
% javac Test.java
% java Test 5 100 200
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

```
//用例：计算标准输入的数字的平均值
%java Average //运行java程序
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

### 重定向与管道

将标准输出**重定向**到一个文件中:

```
% java RandomSeq 1000 100 200 > data.txt
```



可以使用**重定向**标准输入以使StdIn从文件中（而不是终端应用程序中）读取数据：

```
% java Average < data.txt
```

将一个程序的输出重定向为另一个程序的输入叫做管道：

```
% java RandomSeq 1000 100 200 | java Average
```

