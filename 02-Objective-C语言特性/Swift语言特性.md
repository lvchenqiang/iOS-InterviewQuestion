### Swift 语言特性

### class 与 struct的区别

相同点:
* 都能定义property、method、initializers
* 都支持protocol、extension

不同点:
* class是引用类型；struct是值类型
* class支持继承；struct不支持继承
* class声明的方法修改属性不需要mutating关键字；struct需要
* class没有提供默认的memberwise initializer；struct提供默认的memberwise initializer
* class支持引用计数(Reference counting)；struct不支持
* class支持Type casting；struct不支持
* class支持Deinitializers；struct不支持






参考链接:

[掘金](https://juejin.im/post/6844903775816155144)
  
[ClassesAndStructures](https://docs.swift.org/swift-book/LanguageGuide/ClassesAndStructures.html)


### oc的协议与swift协议的区别

共同点:
* 本质都是抽取不同类的共同方法和属性（声明），供遵循协议的类或对象使用。
* 都可以通过定义协议实例deleagate，来实现委托代理模式

类比继承的概念，继承父类的方式比较险隘，子类和父类只能为同一基类，且方法都有实现，需在子类中override，并不能把方法和属性完全独立出来，且不能多继承


不同点:
OC中的协议：
更单纯的受限于委托代理的含义，多用于跨类的传值和回调通知

Swift的协议：
1、Swift可以通过协议 extension 扩展，缺省实现协议的方法（OC不行）。

2、定义属性方法

3、Swift是面向协议编程，其思想是通过抽取不同类中的相同方法和属性，实现模块化减少耦合。

4、Swift的协议不需要单独声明协议对象（**id delegate **）和指定代理（ delegate = self ），只需要遵循协议的类实现声明，或使用协议的缺省实现

