---
title: Java基础
date: 2019-10-16 12:14:21
updated: 2020-10-06 16:53:21
tags:
    - java
    - thread
    - collections
    - extends
    - interface
categories: algorithm
---
# 1. 类型转换

> 在算法题中经常需要进行类型的转换，这里总结常见的类型之间互相转换的方式

- int to String

  - `String str = num + "";`

  - `String str = String.valueOf(num);`

- Integer to String

  - `String str = String.valueOf(i);`

- String to int
  - `int num = Integer.parseInt(str);`
- String to Integer
  - `Integer i = Integer.valueOf(str);`
- Integer to int
  - `int num = i.intValue();`
- int to Integer
  - `Integer i = new Integer(num);`

- char to int
  - `int num = c - '0'; // 计算出和0的ACill值的差值`
- int to char
  - `char c = num + '0';`
# 2. 访问控制权限

​	Java中有四种权限，访问限制从大到小依次为private,default,protected,public。具体的访问权限范围可见如下表：

| 权限      | 类内 | 同包 | 异包子类 | 异包非子类 |
| :---------: | :----: | :----: | :--------: | :----------: |
| private   | √    | ×    | ×        | ×          |
| default   | √    | √    | ×        | ×          |
| protected | √    | √    | √        | ×          |
| public    | √    | √    | √        | √          |

# 3. 继承、接口和多态

## 继承

​	继承就是保持已有类的特性而构造新类的过程。继承后子类能够使用父类中定义的变量和方法。

​	Java的继承是单继承，一个类只能同时拥有一个父类。如果想要让一个类同时继承多个父类的特性，可以选择串联继承，即A类作为B类的父类、B类作为C类的父类，这样C类就一定程度上拥有了A类和B类的特性。

​	继承的关键词是extends，不用该关键词则默认继承Object类。

## 接口

​	若想让一个类同时继承多个类，可以选择使用接口。当父类非常抽象的时候，我们可以选择将它定义为接口。接口只能定义静态变量和抽象方法且不需要定义构造函数，具体功能需要由子类去继承和扩展。

​	接口的定义关键词是interface，实现关键词是implements。

## 多态

​	多态指的是一个接口在传入不同实例时会执行不用的操作。原理就是当父类指向不同子类调用同一个方法的时候，会根据子类是否改写该方法来实现不同的功能（如果父类没有该方法则会报错）。具体如下：

```java
public class Father {
    private int a;
    public Father(int a) {
        System.out.println("This is Father constructor!");
        this.a = a;
    }
    void fun1(int aa) {
        System.out.println("Father fun1:"+aa);
    }
    int showA() {
        return a;
    }
}
public class Son extends Father {
    public Son(int a) {
        super(a);
        System.out.println("This is Son constructor!");
    }
    public void fun2() {
        System.out.println("son fun2");
    }
}
public class Daughter extends Father {
    public Daughter(int a) {
        super(a);
        System.out.println("This is Daughter constructor!");
    }
    public void fun1(int aa) {
        System.out.print("Daughter fun1\n");
        super.fun1(aa);
    }
}
public class funTest {
    public static void main(String[] args) {
        Father s = new Son(1);
        // This is Father constructor!
        // This is Son constructor!
        Father d = new Daughter(2);
        // This is Father constructor!
        // This is Daughter constructor!
        s.fun1(s.showA());
        // Father fun1:1
        // s.fun2();
        // 报错
        d.fun1(d.showA());
        // Daughter fun1
        // Father fun1:2
    }
}
```
# 4. 使用Collections.sort()排序

> 在java中经常需要对list进行排序，而直接调用Collections.sort()相比手动写排序函数则要方便许多。本篇将详述该函数的使用方法。

## 1. Collections.sort()排序原理

​	事实上`Collections.sort()`方法底层就是调用的Arrays.sort方法，而Arrays.sort使用了两种排序方法，快速排序和优化的归并排序。

​	快速排序主要是对那些基本类型数据（int,short,long等）排序， 而归并排序用于对Object类型进行排序。
使用不同类型的排序算法主要是由于快速排序是不稳定的，而归并排序是稳定的。这里的稳定是指比较相等的数据在排序之后仍然按照排序之前的前后顺序排列。对于基本数据类型，稳定性没有意义，而对于Object类型，稳定性是比较重要的，因为对象相等的判断可能只是判断关键属性，最好保持相等对象的非关键属性的顺序与排序前一致；另外一个原因是由于归并排序相对而言比较次数比快速排序少，移动（对象引用的移动）次数比快速排序多，而对于对象来说，比较一般比移动耗时。
​	此外，对大数组排序。快速排序的sort()采用递归实现，数组规模太大时会发生堆栈溢出，而归并排序sort()采用非递归实现，不存在此问题。

​	具体来说，使用`Collections.sort()`排序首先先判断需要排序的数据量是否大于60：
​		**小于60**：使用插入排序，插入排序是稳定的
​		**大于60**：根据数据类型选择排序方式：
​			**对于基本类型**：使用快速排序。因为基本类型都是指向同一个常量池不需要考虑稳定性。
​			**对于Object类型**：使用归并排序。因为归并排序具有稳定性。
​	另外，不管是快速排序还是归并排序。在二分的时候小于60的数据量依旧会使用插入排序

​	所以，可以看出`Collections.sort()`是在对于所要排序的数据类型和大小进行了充分判断以后再选择最优的排序方法进行排序的。我们完全可以放心地把自己的数据交给他去排序而不用担心性能上有太大的问题。

## 2. 使用Collections.sort()来排序

>  使用`Collections.sort()`要求被排序变量类型实现了Comparable接口或者有Comparator比较器。

### 实现Comparable接口

首先在要排序的类中实现Comparable接口:

```java
public class DataForm implements Comparable<DataForm> {
    private String name;
    private Integer value;
    public DataForm(String name, int value) {
        this.name = name;
        this.value = value;
    }

    public String getName() {
        return name;
    }

    public Integer getValue() {
        return value;
    }

    @Override
    public String toString() {
        return "name:"+name+"--value:"+value; // 重写转字符串方法，方便展示
    }

    @Override
    public int compareTo(DataForm a) {
//        return value-a.getValue(); // 比较value大小，升序
//        return a.getValue()-value; // 比较value大小，降序
//        return value.compareTo(a.getValue()); // 调用Integer的compareTo进行比较，升序
        return a.getValue().compareTo(value); // 调用Integer的compareTo进行比较，降序
    }
}
```

然后在代码中便可以直接使用`Collections.sort()`来对list进行排序：

```java
ArrayList<DataForm> a = new ArrayList<>();
a.add(new DataForm("a", 1));
a.add(new DataForm("b", 2));
a.add(new DataForm("c", 3));
a.add(new DataForm("d", 4));
a.add(new DataForm("e", 5));
System.out.println(a.toString());
// [name:a--value:1, name:b--value:2, name:c--value:3, name:d--value:4, name:e--value:5]
Collections.sort(a);
System.out.println(a.toString());
// [name:e--value:5, name:d--value:4, name:c--value:3, name:b--value:2, name:a--value:1]
```

### 使用Comparator比较器

如果被比较的类没有实现Comparable接口，也可以在`Collections.sort()`的第二个参数位直接传入一个Comparator比较器来实现排序的功能：

```java
ArrayList<DataForm> a = new ArrayList<>();
a.add(new DataForm("a", 1));
a.add(new DataForm("b", 2));
a.add(new DataForm("c", 3));
a.add(new DataForm("d", 4));
a.add(new DataForm("e", 5));
System.out.println(a.toString());
// [name:a--value:1, name:b--value:2, name:c--value:3, name:d--value:4, name:e--value:5]
Collections.sort(a, new Comparator<DataForm>() {
    @Override
    public int compare(DataForm dataForm, DataForm t1) {
        // return dataForm.getValue()-t1.getValue(); // 比较value大小，升序
        // return t1.getValue()-dataForm.getValue(); // 比较value大小，降序
        // return dataForm.getValue().compareTo(t1.getValue()); // 调用Integer的compareTo进行比较，升序
        return t1.getValue().compareTo(dataForm.getValue()); // 调用Integer的compareTo进行比较，降序
    }
});
System.out.println(a.toString());
// [name:e--value:5, name:d--value:4, name:c--value:3, name:b--value:2, name:a--value:1]
```

---

> 使用Comparable还是Comparator在性能上并没有太大的差别。前者是在类内进行实现，完成以后该类边成为了一个可比较的类了。但需要修改源代码，在很多情况下并不通用。而后者不需要修改源代码，用户可以自己实现比较的逻辑，并且可复用。因此使用哪种方式还是取决于实际的使用环境。

# 5. Java多线程

> Java多线程的运用十分广泛，用好了可以大大提高程序的运行效率。但多线程之间往往会产生各种冲突，学会如何让多线程互相协作，高效率的完成工作，也是一门学问。本篇将持续更新，讲解自己在日常使用中了解到的多线程的经验和小窍门。

## 1. 多线程基本语法

Java中的多线程主要有两种启动方法，直接继承**Thread**类并调用start()方法或者继承**Runnable**接口。

### Thread

```java
public class thread_test extends Thread {
    private int i;
    @Override
    public void run() {
        for (i=0; i<5; i++) {
            System.out.println(getName()+" "+i);
        }
    }
    public static void main(String[] args) {
        thread_test t = new thread_test();
        t.start();
        t.start(); // 报错
    }
}
```

由于Java**单继承**的特性，利用**Thread**操作多线程没有办法重复调用一个线程。因此程序的复用性较差。

### Runnable

**Runnable**接口则完全没有这方面的问题。

```java
public class runnable_small_test implements Runnable {
    private int i;
    @Override
    public void run() {
        for (i=0; i<5; i++) {
            System.out.println(Thread.currentThread().getName()+" "+i);
        }
    }
    public static void main(String[] args) {
        runnable_small_test t = new runnable_small_test();
        new Thread(t).start();
        new Thread(t).start(); // 不会报错
    }
}
```

## 2. 使用synchronized关键词来防止线程竞争同一资源

> 如果有两个或两个以上线程同时对一个线程不安全的变量进行操作就可能出现不可预知的错误。比如同时对一个整型做`++1`的操作，可能该整型最后只进行了一次`++1`。对于这种情况，可以使用`synchronized`、`wait()`和`notify()`来增加同步块并控制线程的先后顺序防止出现竞选条件。

下面是我使用**Runnable**接口来实现生产者与消费者的例子。

```java
public class  runnable_test {
    public static void main(String[] args) {
        product i = new product(10);
        consume targetRunnable_consumer = new consume(i);
        produce targetRunnable_producer = new produce(i);
        new Thread(targetRunnable_consumer, "consumer").start();
        new Thread(targetRunnable_producer, "producer1").start();
        new Thread(targetRunnable_producer, "producer2").start();
    }
}
class product {
    public int i;
    public product(int i) {
        this.i = i;
    }
}

class consume implements Runnable {
    private product i;
    public consume(product i) {
        this.i = i;
    }
    @Override
    public void run() {
        while (true) {
            synchronized (i) {
                if (i.i==0) {
                    try {
                        i.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                } else {
                    i.i--;
                    System.out.println(Thread.currentThread().getName()+" "+i.i);
                }
            }
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
class produce implements Runnable {
    private product i;
    public produce(product i) {
        this.i = i;
    }
    @Override
    public void run() {
        while (true) {
            synchronized (i) {
                i.notify();
                i.i++;
                System.out.println(Thread.currentThread().getName()+" "+i.i);
            }
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

具体来说，在`synchronized`规划的代码块中，他所指定的变量**i**只能有一个线程进行操作。

而`wait()`和`notify()`就类似**PV操作**，某线程如果调用了`wait()`，他就会陷入阻塞状态直到其他线程调用`notify()`了才能继续运行。多线程间可以通过这种方式来控制对于公共变量的访问顺序。

另外，每次使用`notify()`只会使一个调用了`wait()`的进程继续工作。如果有多个线程调用了`wait()`，你想一次性让他们都继续运作，可以使用`notifyall()`。

> 值得注意的是，`synchronized`指定的同步变量不能是封装类，否则同步功能会失效。因为封装类提供了自动装箱的功能，在对封装类进行操作以后其实变量的HashCode已经发生了变化，synchronized自然也不是同一个对象实例，Integer的源码，如下所示：

> ![img](http://dl.iteye.com/upload/picture/pic/137529/c092c1c9-e167-3132-b865-2f4778b7d91b.png)