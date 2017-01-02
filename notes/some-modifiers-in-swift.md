# Swift 中的常用修饰符

在 Swift 中，与 class、struct、property 相关都有诸多修饰符，很多时候用对了修饰符可以避免很多的潜在问题，让代码更严谨。本文即用来阐明部分修饰符的含义以及具体应用情境。

作者：[@CaiYue_](http://weibo.com/caiyue233)

---

## 访问控制相关

首先我们要了解 Swift 中模块(module)的含义。模块指的是一个单独的代码分发单元，例如我们平时使用中经常会 import xxx，那么这个 xxx 就是一个 module。每一个编译的 target 也被看待成 Swift 中单独的一个模块。

还有一个概念，就是源文件(source file)，源文件指的就是模块中每个单独的文件。尽管在一般情况下我们会在不同的源文件中定义各自独立的类型，但是有时也可以在一个源文件中给不同的类型、函数等做定义。

Swift 中提供了五种不同的访问等级。

1. Open 和 public，被这两个词修饰的实体可以在该 module 其它任何源文件中被访问到，当然，在别的 module 如果 import 了该 module 同样也可以访问到。
2. Internal 修饰符只可以在本 module 中被访问。
3. File private 将被修饰的实体只在自己定义的源文件中被访问。
4. Private 修饰符将被修饰的实体的使用范围限定在 enclosing declaration 里。

按严格程度分，显然 open 是最松的，private 最紧。

Open 只被用在 class 以及 class member 上，和 public 的区别在于：
- 被 public 修饰的 class，以及比 public 更严格的修饰符修饰的 class，它们的子类只能在它们所在的 module 中进行定义。
- 同样，被 public 修饰的 class member，以及比 public 更严格的修饰符修饰的 class member，只能在它们所在的 module 中被重写。
- Open 修饰符修饰的可以在本模块的任何地方被子类化，也可以在外部 module 被 import 过后，进行子类化。
- Open 修饰符修饰的 class member 同上，除了可在本模块中被重写，也可以在外部 module 被 import 过后被重写。






