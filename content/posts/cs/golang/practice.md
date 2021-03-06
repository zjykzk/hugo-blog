+++
author = "zenk"
slug = ""
tags = ["program","design"]
draft = true
categories=["cs"]
title="golang的实践"
description="golang的一些实践习惯。"
date="2018-09-26T10:22:39+08:00"

+++

## 原则

Clarity：强调Readability。

Simplicity：强调系统简单性。

Productively：解决问题没有非本质的障碍，没有等待。

## 命名

命名很重要。软件最重要的事情是应付复杂度，那么可读性就变得非常重要，基本上定义了软件的质量。命名就是影响可读性的关键。

[好的名字的特点](https://talks.golang.org/2014/names.slide#3)：

1. 一致（容易猜测）。
2. 短小（容易书写）。
3. 精确（容易理解）。

**长度**

变量名的长度和它使用的距离成正比。

**规则**

1. 使用驼峰命名
2. 缩写字母全部大写。

**一些命名习惯**

1. 名字尽量短小，主要包括参数，局部变量，接受者(receiver)，例如：`i > index;r > reader;b > buffer`。

2. 数据库操作的常规命名：Insert/Remove/Update/Find。
3. 接口，只有一个方法，以"er"结尾，如果有多个那就需要尽量描述接口的名字。
4. 配置，变量用`conf`，类型用`XXXConfig`。
5. 创建对象统一用New，不用Create。
6. 接受者(receiver)，使用1-2个字符，每个方法的命名要一致。
7. 返回值命名的目的就是为了说明返回内容的意义。
8. 错误，类型定义以`Error`结尾，例如：`type FooError struct{}`；错误变量以`err/Err`开头`ErrFoo := FooError{}`。
9. 包，小写，短小精悍，通过包名知道包的作用，避免common/utils这样的包名；不能有复数的包名。

https://blog.golang.org/package-names
https://rakyll.org/style-packages/
https://github.com/bketelsen/talks/blob/master/slides/gcru18-best.md

## 注释

// TODO

## 错误处理

1. 不好忽略每个错误，及时忽略需要注明原因。
2. 调用的接口返回错误，当返回该错误时最好做个封装指明错误的来源，方便做错误跟踪。
3. 每个模块返回的错误，在错误信息里面包含模块信息，一般会定义一个辅助方法。

## 包

**组织**

1. 同一个包中使用多个文件，每个文件负责不同的逻辑。
2. 把类型定义在它被使用的附近。
3. 按照功能组织代码，还不是类型。
4. 不导出main这个包中的任何东西，因为它是起点。
5. internal包，如果子包的接口不暴露给外面，使用internal包来保护这些接口。

**导入**

1. 使用虚拟URL，就是给一个包定义一个导入的路径，如果不按这个路径来就会出错，这个可以避免多副本的问题。
2. 包名应该和目录名一样。
3. 引用一个包的变量的时候，使用包名作为前缀，也就是说避免全部导入：`import . "math"`。
4. 导入干净的包路径，导入路径中不能有src/pkg。

## 接口

接口的目的是抽象与解耦。接口定义的位置在使用者的源代码中，而不是实现者，这样的好处是使用者和实现者耦合性最低。

接口定义位置的一些例外：

1. `sort`包下面的`Interface`，类似这样的抽象数据结构。

2. 接口定义中引用了自己，比如定义一个Comparable接口。

   ```
   type Comparable interface {
     Compare(o Comparable) int
   }
   ```

## 并发

**channel**

1. 明确channel的收发角色。
2. 向channel发送的数据时候，需要考虑消费者很慢的情况，需要文档说明。
3. 向channel发送的数据量没有限制的情况，考虑通过参数把channel传过来，这样可以方便用户自己调优。
4. 向channel发送的数据量有限制的情况，考虑返回一个拥有buffered的channel，因为自己能够控制大小。

## 值vs指针

在传递参数和返回参数的时候，如果不确定是用值还是指针，优先考虑值。因为，指针可能会有以下几个问题：

1. 大概率是在堆上分配的。
2. 糟糕的局部引用。
3. 编译器会对解除指针时做额外的检查。
4. Cache层拷贝一堆对象，可以粗略的认为和拷贝一指针效率差不多，因为cache层和主存之间中以固定大小的数据进行传输。x86下面是64字节。

## 项目结构

```
project-layout
├── api
├── assets
├── build
├── cmd
├── configs
├── deployments
├── docs
├── examples
├── githooks
├── init
├── internal
├── LICENSE.md
├── Makefile
├── pkg
├── README.md
├── scripts
├── test
├── third_party
├── tools
├── vendor
├── web
└── website
```

细节参考https://github.com/golang-standards/project-layout。

### test目录

mock代码目录：test/mocks/<entity>/<entity>.go

测试数据目录：test/testdata