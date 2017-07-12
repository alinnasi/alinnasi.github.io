---
title: Golang学习笔记：如何初始化结构体
category: Golang
---

## 前言

刚开始学习Golang。虽然在大学的时候写了很多C程序，自认对C语言还是比较熟悉的，但写了两年的Java后再重新学习系统编程语言，有时候脑子还是转不过弯。

学习笔记里的东西虽然简单，但始终还是一些知识点的总结，之后脑子卡壳的时候可以拿出来翻阅，避免犯下低级错误。

这篇博客就来讲讲初始化结构体的几种方式。

## 初始化结构体的几种方式

### 使用 var

```
var t T
t.a = 1
t.b = "hello"
```

使用这种方式初始化结构体，对于长期进行Java开发的人来说，可能第一反应是变量t的值应该为Null（我就是这样的，只能说C语言都还给老师了），但实际上Golang中是有显式的指针类型的，指针类型才对应Java中的引用类型。

因为结构体类型并不是指针，因此当将一个结构体传给一个函数时，这时候是传值而不是传参。

### 使用 new

```
var t *T
t = new(T)

// 使用初始化声明
t := new(T)

t.a = 1
// 虽然看起来多此一举，但是合法的
(*t).b = "hello"
```

使用new函数给一个结构体分配内存，并返回指向内存的指针。无论是结构体还是结构体指针，都使用点号访问符来访问结构体中的字段，这一点和C/C++不一样（在C/C++中，要通过一个结构体指针访问结构体中的字段时，需要使用“->”访问符）。

### 混合字面量语法

```
type T struct {
    a int
    b int
}

func main() {
    // a = 1, b = 2
    var s1 T = T{1, 2}

    // a = 0, b = 0
    var s2 T = T{}

    // 下面这种写法会报错，不能只有部分初始值
    // var s3 T = T{1}

    // a = 1, b = 2
    var s4 T = T{a: 1, b: 2}

    // a = 0, b = 2
    var s5 T = T{b: 2}

    // a = 1, b = 2
    var p *T = &T{1, 2}
}
```





