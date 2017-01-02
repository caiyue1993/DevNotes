在继续阅读之前，关于 Swift 中的初始化，提出几个问题：

- 你能说出 designated initializer 和 convenience initializer 的区别吗？
- class type 和 value tyle 在初始化的时候有什么区别？ 
- 你知道以闭包方式设定属性默认值时需要注意的事项吗？

和之前一样，在本篇中将以文字配合代码的形式讲解。

# 初始化 (Initialization)
此处比较基础，谈三点：

- Swift 中的初始化方法是没有返回值的。不论是类 (class) 还是结构体 (struct)，在其给创建实例完成之后，都必须给实例的每个存储属性 (stored properties) 设置一个合适的初始值。

- 在设置属性初始值和设置默认值的时候，都不会触发属性的 observers。

- 在类类型中，你可以不写明 init 方法，但是每个值都必须有默认值才行，这样 Swift 才会默认为你生成默认初始化器；如果是值类型就比较随意，你可以在不为每个值都赋默认值的情况下同样不写明 init 方法，因为 Swift 会默认为你生成 memberwise 初始化方法。（如果不懂可以继续往下看）

# let 声明的常量值可以在 init 方法中被赋值
let 声明的常量如果没有默认值的化，那么它可以在 init 方法中被赋值。当然，在该常量的生命周期里，只可能被赋值这一次，其它地方均不能再修改它。

# 指定初始化器 （Designated Initializer）
如果类或结构给所有的属性值都提供了默认的值，并且没有手动提供任何一个初始化器，那么 Swift 将会自动生成一个指定初始化器（当然所有的属性值都被设定为默认值）。例如：

```Swift
class Date {
    let year: Int = 1993
    let month: Int = 12
    var day: Int?
}
在上面的例子中，每个值都有了一个默认值(对于属性 day，Swift 文档中有定义，如果你定义了一个 Optional 类型的**变量**，系统默认赋值为 nil)，那么 Swift 就给这个类自动生成了默认初始化方法，在外部我们可以通过 Date() 来对该类进行初始化。 
```

另外，对于 ** 结构体 ** ，Swift 还有一个特别照顾的策略：如果结构体中没有提供初始化方法，无论属性值有没有提供默认值，Swift 会自动为结构体生成一个 memberwise 初始化器。例如：

```Swift
struct Date{
    let year: Int = 1993
    let month: Int
}
那么在外部，我们可以通过 Date(month: 12) 的方式初始化该结构体。

```

如果在值类型中曾经手动提供了初始化方法，那么你将不再能调用到 memberwise 初始化方法。例：

```Swift
struct Date{
    
    let year: Int = 1993
    let month: Int
    
    init() {
        self.month = 12
    }
}
在这种情况下你只能通过定义过的 Date() 的方式来初始化了。
```

* 然而，如果你想要同时拥有默认初始化器和自定义初始化器的话，你可以考虑将自定义初始化器放在 extentsion 中。*

# 关于初始化代理（Initializer Delegation）
初始化代理的目的是减少写重复的代码。在这里，值类型（value type，结构体和枚举）和类类型 (class type) 不一样：

值类型并不支持继承，所以它们在初始化的时候只能调用它们自己的其它初始化方法。

类类型的话，由于存在继承的机制，所以需要保证所有的属性都得到合适的初始化值。下面我们来谈：

## 类的继承和初始化
类的所有属性值，包括从父类中继承来的，都必须在初始化过程中提供初始值。
Swift 中有两类初始化类型，指定初始化方法 (designated Initializer) 和便捷初始化方法 (convenience Initializer)。

### 指定初始化方法
指定初始化方法是类的基础初始化器，一个指定初始化方法会将该类生命的所有属性值初始化，并且调用合适的父类初始化器，从而持续初始化过程（通过传递到父类链）的方式。一般而言一个类的指定初始化方法只有一个。（但只是一般而言。）
每个类都必须有一个指定初始化方法，大多情况下，这个要求都是因为子类会继承父类的指定初始化方法，从而满足该要求。

### 便捷初始化方法
一般在不是必须的情况下，我们可以不提供便捷初始化方法。便捷初始化方法是用来满足特定的要求的。

### 两类初始化方法的关系
在外形上，便捷初始化方法在 init 前多了关键字 convenience。与此同时，Swift 要求以下三条规则：

1. 一个指定初始化方法中必须调用它的直接父类中的指定初始化方法。（如果没有父类，那么忽略此条）
2. 一个便捷初始化方法必须调用本类中另一个初始化方法。
3. 一个便捷初始化方法必须 ** 最终 ** 调用的还是指定初始化方法。

### override 关键字
与 Objective-C 不同，默认情况下 Swift 子类是不继承父类的初始化方法的。你如果想要在子类中拥有和父类一样的初始化方法，那么你必须加上 override 关键字。
举个例子：

```Swift
class Vehicle {
  var numberOfWheels = 0
  var description: String {
    return "\(numberOfWheels) wheel(s)"
  }
}
```

根据之前提到的我们知道，“如果类或结构给所有的属性值都提供了默认的值，并且没有手动提供任何一个初始化器，那么 Swift 将会自动生成一个默认初始化器。” 因此该类会自动生成一个默认的初始化器。并且其实默认的初始化器总是该类的指定初始化方法。即我们可以这样调用：

```Swift
let vehicle = Vehicle()
print("Vehicle:\(vehicle.description)")
```
这时如果我们打算自定义它的子类 Bicycle：

```Swift
class Bicycle: Vehicle {
  override init() {
    super.init()
    numberOfWheels = 2
  }
}
```

在这里 super.init() 必须写在 numberOfWheels 的前面，保证 numberOfWheels 属性在父类中成功分配内存初始化，从而之后可以被修改。（当然，只能修改继承父类中的 var 类型的属性）

## 自动初始化方法继承
在上一节提到了，默认情况下子类是不继承父类的初始化方法的。然而，如果满足一些特定条件，子类是会自动继承父类的初始化方法的。这也就意味着，在很多相同的场景下你不需要再去重复写初始化方法了。

假设你已经给子类中的新属性赋默认值了，那么这时有下面两条规则：
1. 如果该子类没有提供任何指定构造器，它将自动继承父类**所有的**指定初始化器。
2. 如果该子类拥有父类所有的指定初始化器（包括通过第一条规则中获取来的指定初始化器），那么该子类自动继承父类的所有便捷初始化方法。

上述两条规则同样适用于子类加上自己的便捷初始化器的情形。例如：

![convenient initializer](https://github.com/caiyue1993/DevNotes/blob/master/images/initializer-in-swift.png)

值得注意的一点，在 Bicycle 的便捷初始化方法里，self.init(color:) 方法不能改为 super.init(color:)，原因是“一个便捷初始化方法必须调用本类中另一个初始化方法。”

## init? 和 init!
有些时候在初始化的过程中，由于诸多原因，可能会失败（例如输入了不合适的初始值、外部资源缺失等，还有其它可能阻止正常初始化的原因）。这时我们可以通过 init? 来初始化（当然，init？ 初始化不能和原有的初始化方法拥有相同的参数和名字，原因是这样编译器就不知道执行哪个了。）
一个可错误初始化器会创建一个 Optional 类型的初始值。我们可以通过 return nil 来表示（indicate）初始化错误。例如：

```Swift
struct Animal {
    let species: String
    init?(species: String) {
        if species.isEmpty {
            return nil
        }
        self.species = species
    }
}
```

在上述情况下，如果 let anonymousCreature = Animal(species: "") 那么它的值为空。

再举个例子，我们可以通过下面的方式给枚举类型增加可错误初始化器。

```Swift
enum TemperatureUnit {
    case kelvin, celsius, fahrenheit
    init?(symbol: Character) {
        switch symbol {
        case "K":
            self = .kelvin
        case "C":
            self = .celsius
        case "F":
            self = .fahrenheit
        default:
            return nil
        }
    }
}
```

值得一提的是，拥有 raw values 的枚举类型会自动生成一个可错误初始化器 init?(rawValue:)，我们用这种方式给上面的枚举进行重写。 

```Swift
enum TemperatureUnit: Character {
    case kelvin = "K", celsius = "C", fahenheit = "F"
}
```

关于可错误初始化器的继承和代理调用，不难，就是描述略复杂，为了避免打消写作的积极性，该部分可以看官方文档，本文这里省略。

我们可以在初始化的时候，init? 与 init! 互相调用。继承的时候，同样可以互相调用。

## Required Initializers 
抱歉，我没有想到比较好的译名。在初始化器前面加上 required 表示，每个子类都必须实现该初始化器。另外，子类在继承的时候，并不需要在该 Required Initializers 前加上 override 关键字了。然而，如果你满足继承初始化器的条件，你也可以不具体实现它，因为它已经被继承了。

## 以闭包(closure)或者函数的方式设定默认值
这种方式在实际使用中应用的最多。如果我们要对某个存储属性进行个性配置或者优化，那么可以通过闭包或者函数的方式。（作者：个人觉得这么写满足软件工程“高内聚”的特点，因此建议使用）下面是一个范例(skeleton)：

```Swift
class SomeClass {
    let someProperty: SomeType = {
       
        return someValue
    }()
}
```

注意，在大括号外面还有一对小括号，这告诉 Swift 立即执行这个闭包。如果你不加上这个括号，就表示将该闭包赋值给该变量，这明显是错误的。但是有一点值得注意：
当闭包执行的时候，实例的其它地方都还没有初始化呢。因此在闭包中你不能取用其它属性值，就算其它属性值有默认值也不行。另外你也不能在闭包中使用 self 属性，更调用不了实例方法，理由同上。

注：本文根据 [The Swift Programming Language(Swift 3)](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/)，如想了解更多，请直接阅读官方文献。


*本文版权所有，如需转载，请告知原作者并注明出处*
