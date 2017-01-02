# Swift 中的常用修饰符

在 Swift 中，与 class、struct、property 等相关实体都有诸多修饰符，用对了修饰符可以避免很多的潜在问题，让代码更严谨。本文即用来阐明部分修饰符的含义以及具体应用情境。

作者：[@CaiYue_](http://weibo.com/caiyue233)

==========

## 访问控制相关

首先我们要了解 Swift 中模块 (module) 的含义。模块指的是一个单独的代码分发单元，例如我们平时使用中经常会 import xxx，那么这个 xxx 就是一个模块。每一个编译的 target 也被看待成 Swift 中单独的一个模块。

还有一个概念，就是源文件(source file)，源文件指的就是模块中每个单独的文件。尽管在一般情况下我们会在不同的源文件中定义各自独立的类型，但是有时也可以在一个源文件中给不同的类型、函数等做定义。

Swift 中提供了五种不同的访问等级。

1. **Open** 和 **public**，被这两个词修饰的实体可以在该模块其它任何源文件中被访问到，当然，在别的模块如果 import 了该模块同样也可以访问到。
2. **Internal** 修饰符只可以在本模块中被访问。
3. **File private** 将被修饰的实体只在自己定义的源文件中被访问。
4. **Private** 修饰符将被修饰的实体的使用范围限定在该实体被声明的“闭包”里。

按严格程度分，显然 open 是最松的，private 最紧：open，public > internal > file private > private

Open 只被用在 class 以及 class member 上，和 public 的区别在于：
- 被 public 修饰的 class，以及比 public 更严格的修饰符修饰的 class，它们的子类只能在它们所在的模块中进行定义；Open 修饰符修饰的可以在本模块的任何地方被子类化，也可以在外部模块被 import 过后，进行子类化。
- 同样，被 public 修饰的 class member，以及比 public 更严格的修饰符修饰的 class member，只能在它们所在的模块中被重写；Open 修饰符修饰的 class member，除了可在本模块中被重写，也可以在外部模块被 import 过后被重写。

在有些实体会互相嵌套的时候，使用访问控制有什么特别的讲究吗？例如在一个被 private 修饰的 class 中声明被 public 修饰的属性可以吗？

答案是不行。掌握一条原则，没有实体可以定义在更严格的实体中。

下面是一些可能有用的小提普斯：

- 默认修饰符是 internal。
- 如果一个实体声明是 private/file private，那么它的实体中的成员（例如属性、方法、初始化器以及下标）都会默认被修饰成 private/file private;如果一个实体声明成 internal/public，那么该实体中的成员都会默认被修饰成 internal。如果你想让 public 修饰的成员都是 public 的，那么则需要一个一个的自己手动设置。
- 对于 getter 和 setter，我们可以给 setter 比 getter 更严格的访问权限。例如：

```Swift
struct TrackingString {
	private(set) var numberOfEdits = 0
	var value: String = "" {
		didSet {
			numberOfEdits += 1
		}
	}
}
```
分析一下，TrackingString 类和 value 都默认修饰符为 internal，而 numberOfEdits 被修饰成 private(set) 表示这个属性的 getter 仍然是默认的 internal，而 setter 是 private 的。

关于访问控制在初始化器，协议，扩展等的用法，就不在此细谈了，具体可参照 《The Swift Programming Language》 的 [Access Control 章节](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html#//apple_ref/doc/uid/TP40014097-CH41-ID3)




