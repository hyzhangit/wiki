
## 数据类型



## 基本语法

### 泛型
Scala泛型使用`[]`表示, 样例:
```Scala

```

## 特殊概念

### 伴生对象

### 柯里化

方法可以定义多个参数列表，当使用较少的参数列表调用多参数列表的方法时，会产生一个新的函数，该函数接收剩余的参数列表作为其参数。这被称为柯里化。--[官方教程](https://docs.scala-lang.org/zh-cn/tour/multiple-parameter-lists.html)

```Scala

```

### 高阶函数

高阶函数是指使用其他函数作为参数、或者返回一个函数作为结果的函数。在Scala中函数是“一等公民”，所以允许定义高阶函数。这里的术语可能有点让人困惑，我们约定，使用函数值作为参数，或者返回值为函数值的“函数”和“方法”，均称之为“高阶函数”--[高阶函数](https://docs.scala-lang.org/zh-cn/tour/higher-order-functions.html)。

最常见的一个例子是Scala集合类（collections）的高阶函数map：
```Scala
val salaries = Seq(20000, 70000, 40000)
val doubleSalary = (x: Int) => x * 2
val newSalaries = salaries.map(doubleSalary) // List(40000, 140000, 80000)
```

### trait

### 模式匹配





