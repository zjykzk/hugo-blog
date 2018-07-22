+++
author = "zenk"
tags = ["编程","设计"]
draft = false
categories=["cs"]
title="接口在哪里定义？"
description="讨论接口定义放在哪里比较合适"
date="2018-07-15T15:11:11+08:00"

+++

接口放在哪里决定了源代码依赖问题。因此，依赖是接口定义唯一考量，其他问题都可以归结为依赖问题，而定义的包永远是被依赖包。

接口定义的位置有三种情况：

1. 使用者
2. 实现者
3. 单独一个第三方位置

**放在使用者这边**，那么实现者依赖使用者的接口定义。

**好处**：可以并行开发，尤其是类似golang这样的语言，实现一接口不需要引用具体的接口定义，即使在必须引用的开发语言里面也只需要实现相关的接口，集成的时候加上是很简单的。

**坏处**：在实现者依赖接口定义源代码的情况下，实现者代码要提出来重用，必须要得要包含使用者的接口定义

这样的方式比较适合多个使用者，单个实现者的情况。

**放在实现者这边**，那么使用者依赖实现者的接口定义。

**好处**：实现者是一个独立的包，可以很方便的重用。

**坏处**：使用者开发的时候需要引用实现者的接口定义，增加并行开发的难度，这里可以自己mock接口，集成的时候改成实现者的接口即可。

这样的方式适合单个使用者，多个实现者情况。

**单独放在第三方位置**

**好处**：定义完接口以后，使用者和实现者都可以并行开发，同时实现者包的重用和使用者解耦。

**坏处**：包的管理变得复杂，包含接口的包会变得很薄

这样的方式适合多个使用者，多个实现者情况。

## 