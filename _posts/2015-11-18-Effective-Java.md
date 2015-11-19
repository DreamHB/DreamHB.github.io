---
layout: post
title: Effective Java 
excerpt: "创建和销毁对象"
modified: 2015-11-18
tags: [java]
comments: true
image:
    feature: sample-image-5.jpg
---

这一章主要是说创建和销毁对象：何时和如何创建对象，何时和如何避免创建他们，如何确保对象在合适的时候被销毁，还有如何管理在对象销毁前的任何可能的清理操作。

#####考虑用静态工厂方法替代构造方法
最普通的方法让一个类允许客户端获取它的实例的方法是提供一个**public**类型的构造函数。有一个不是很广为人知的技术应当被所有的程序员所熟知。一个类可以提供一个公有的静态工厂方法，一个简单的静态方法仅仅只是返回该类的实例。这里有个简单的关于Boolean类的例子（基本数据类型boolean的封装类型）。这个静态方法是从JDK1.4开始添加的，将一个boolean类型的基本值转换成Boolean对象的引用：

```
public static Boolean valueOf(boolean b){
	return (b ? Boolean.TRUE : Boolean.FALSE);
}
```
一个类可以提供给客户端静态工厂方法来替换构造函数或者是作为一种补充。提供一个替代公有构造函数的静态工厂方法既有优点也有缺点。

######优点一：和构造函数不同，他们有名字
如果构造函数的参数不能很好的描述它们所返回的对象，那么一个很好的名字的静态构造方法让一个类更容易使用和客户端代码更容易阅读。举个例子，BigInteger的构造函数BigInteger(int, int, Random), 返回的是一个基本的BigInteger，而如果用BigInteger.probablePrime,那么就表述的更清楚了。

一个类同一种签名的构造函数只能有一个。一个众所周知的方法是调换参数的顺序来避免这个问题。这不是个好办法，因为API的使用者肯定会用错的，他们如果不读文档就不会知道这个API是干嘛的。
因为静态工厂方法有名字，所有他们没有构造函数的限制。如果一个类需要多个相同签名的构造函数，可以考虑使用静态工厂方法，当然，名字需要取好，要亮点突出。（有个疑问，为啥会有这种情况呢？）

######优点二:和构造函数不同，不是每次调用都需要创建新的对象
这允许不变的类使用之前创建的实例或者是创建实例时并缓存起来，然后重复的分发这些实例从而避免重复创建不必要的实例。Boolean.valueOf很好的诠释了这个技术：它从不创建对象。它能极大的提升性能如果相同的对象经常的被请求，特别当这些对象的创建成本很高时。

静态工厂方法的这种重复方法调用返回同一个对象的能力，也可以被用来在任意时间控制实例的存在与否。这么做的原因有俩个：
首先，它允许一个类确保自己是一个单例。其次，它允许一个不变的类能确保没有俩个完全相同的实例。a.equals(b)当且仅当a==b，
如果一个类能做出这样的保证，那么它的客户就能够使用 **==** 而不是 **equals** 这个可能会有很大的性能提升。类型安全的 **ENUM** 实现了这个优化，还有String.intern 用一种有限制的形式实现了这个。 

** 友情提示 **：String.intern 是为了确保此String在内存中只有一个实例

######优点三：和构造函数不同，它可以返回返回值类型的任意子类
这让在选择返回对象的类型时有了极大地扩展性。
这种扩展性的一个应用就是一个API能够在返回对象的时候不用让类公开。通过这种方式隐藏实现的类会产生一个非常紧凑的API。
这个技术导致自身成为基于接口的框架，在这里接口为静态工厂方法返回自然的返回类型。

举个例子，**Collections** 框架有二十个方便的实现了自身接口的方法，提供了不可修改的，同步的 **collection** 等等。这其中最主要的实现是通过静态工厂类暴露出来的一个唯一、非实例化的类(java.util.Collections).返回的所有对象的类都是非公有的。


**未完待续**