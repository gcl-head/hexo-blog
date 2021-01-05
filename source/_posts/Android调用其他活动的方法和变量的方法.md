---
title: Android调用其他活动的方法和变量的方法
date: 2020-04-13 18:49:19
updated: 2020-04-13 18:49:19
tags:
    - android
categories: frontend
---
> 在安卓开发中，随着app的复杂程度上升。会经常出现需要在自己*Activity*中调用别的*Activity*中方法和变量的情况。但因为找不到指向其他*Activity*实例的变量，所以没有办法获取其他*Activity*或者*Fragment*的非静态变量和方法。

## 1. 使用静态关键词

第一种方法是将想要访问的变量和方法都设为静态的。这样就可以直接通过类名进行访问。

```java
private static Pre_class[] pre_classes;
public static Pre_class[] getPre_classes() {return pre_classes;}
public static void setPre_classes(Pre_class[] a) {
    pre_classes = a;
}

// other Activities
MainActivity.setPre_classes(pre_classes);
```

## 2. 为活动创建实例

如果调用的方法或者变量是非静态的，那么第一种方法就不是这么实用了。而且静态也容易造成资源的消耗。因此将要调用的*Activity*的实例赋给该类的一个静态变量则是一个更好的选项。只需要消耗资源定义一个实例对象作为入口，就可以通过他来调用其他*Activity*的非静态方法。

```java
public static A instance=null;
protected void onCreate(Bundle savedInstanceState) {
    instantce = this;
    // ...
}

// other Activities
MainActivity.instance.setPre_classes(pre_classes);
```

---

学会了这种技巧，我们便可以在进入某个*Activity*的情况下操其他*Activity*的变量方法，或者也可以用这种技巧在本*Activity*中的静态方法里操作非静态的方法或者变量了。