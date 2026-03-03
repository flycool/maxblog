---
tags:
  - kotlin
  - android
title: kotlin generic
date: 2022-10-24
---

# 泛型中的子类型

`String` 是 `CharSequence` 的子类型，那么 `List<String>` 是 `List<CharSequence>` 的子类型吗？
给出两个推论：
- 1. 子类型方法接收参数的范围 不得小于 父类型方法
- 2. 子类型方法返回值的范围 不得大于 父类型方法 
- 只有满足了这两个条件，才能无副作用地将程序中父类对象都替换成子类对象（无副作用即是程序符合原有的逻辑）。

# 不变型

有`class A<T>`，而`Type1`是`Type2`的子类，如果`A<Type1>`和`A<Type2>`不存在父子关系，则称 类A 在类型参数上是**不变型的**。
kotlin和java中的类都是不变型的

# 协变

**不变型**描述的是泛型类之间没有子类型关系，泛型类之间还有一种子类型关系叫**协变**。

==**协变**的意思是：类与其类型参数的抽象程度具有相同的变化方向。

换句话说：当类型参数变得更具体时，类也变得更具体。当类型参数变得更抽象时，类也变得更抽象。

Kotlin 中，声明类在类型参数上是协变的，需要添加`out`保留字：

```kotlin
class MyList<out T> {
	fun set(item: T) {} // 报错: Type parameter is declare as "out" but occur at "in" position in type T
	fun get(): T {}
}
```

- 若`T`出现在方法的参数位，称`set(item: T)`**消费类型为`T`的值**。
- 若`T`出现在返回值位时，称`get(): T`**生产类型为`T`的值**。
- 当`T`被`out`修饰后，它只能出现在返回值位，即它只能被泛型类生产而不能被消费。

简单理解：
`out` 字面理解表示为 生产，只能用来输出
`in` 表示消费，只能用来输入

# 逆变

==**逆变**的意思是：类与其类型参数的抽象程度具有相反的变化方向。

换句话说：当类型参数变得更具体时，类却变得更抽象。当类型参数变得更抽象时，类却变得更具体。

```kotlin
class MyList<in T> {
	fun set(item: T) {}
	fun get(): T {} //报错: Type parameter is declare as "in" but occur at "out" position in type T
}
```

当`T`被`in`修饰后，它只能出现在参数位，即它只能被泛型类消费而不能被生产。

# PECS 原则 & POCI 原则
**PECS = producer extends，consumer super**，即如果泛型类生产泛型对象，则使用`<? extends T>`通配符表示协变。如果泛型类消费泛型对象，则使用`<? super T>`通配符表示逆变。

Kotlin 中使用更简单的`out`, `in`表达协变和逆变。所以 PECS 原则在 Kotlin 中可以表述为**POCI 原则**


# 类型投影

out投影 和 in投影在编译器的表现

```kotlin
public interface MutableList<out T> {
	public fun removeAt(index: Int) : T
	public fun add(index: Int, element: Nothing): Unit
}

public interface MutableList<in T> {
	public fun removeAt(index: Int) : Any?
	public fun add(index: Int, element: T): Unit
}

```

`Nothing`是所有类的子类，它也不能被实例化，并且没有子类型。(它已经是最小的了)
`Any?`是所有类的父类 （它已经是最大的了）

**类型投影**：

>将类型投影意味着保留该类型的有些能力，去掉另一些能力。通过类型投影可以动态地改变泛型类的子类型关系。

类型投影通常应用于将**不变型**的泛型类动态地转换成**逆变**或**协变**。

Kotlin 中一共有三种类型投影，总结如下（其中，Group、Dog、Animal都是类名，且 Dog 是 Animal 的子类型）：

|  投影类型   |   投影实例  |   变型 | 继承关系 | 限制 |
|  ----  | ----  | ----  | ----  | ----  |
| out 投影  | Group< out Animal > |协变|Group< Dog > 是 Group< out Animal > 子类|类型参数不能作为方法参数|
| in 投影 | Group< in Animal > |逆变|Group< in Animal > 是 Group< Dog > 子类|类型参数不能作为方法返回值|
|star 投影|Group< * >|--|Group< 任何类型 > 都是 Group< * > 子类|类型参数不能做方法参数也不能做返回值|


泛型的本质是为了 参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类，接口和方法中。
- 适用于多种数据类型执行相同的代码（代码复用）
- 泛型中的类型在使用时指定，不需要强制类型转换（类型安全，编译器会检查类型）

