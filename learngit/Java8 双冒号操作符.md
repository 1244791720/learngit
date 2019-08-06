# Java8 双冒号操作符

## 1. 概览

这篇文章主要讨论Java8 的双冒号操作符(::)的使用场景

## 2. 从 Lambda 表达式到 :: 操作符

Lambda 表达式的代码非常简洁如:

创建一个比较器

```java
Comparator c = (Computer c1, Computer c2) -> c1.getAge().compareTo(c2.getAge());
```

用类型推断

```Java
Comparator c = (c1, c2) -> c1.getAge().compareTo(c2.getAge());
```

用 :: 操作使代码可读性更高

```java
Comparator c = Comparator.comparing(Computer::getAge);
```

## 3. :: 操作符 如何起作用

简单地说，当我们使用方法引用时——目标引用放在分隔符::之前，方法名在分隔符::之后提供。

例如:

```java
Computer::getAge;
```

通过 :: 操作符操作 Computer 类的 getAge 方法：

```java
Function<Computer, Integer> getAge = Computer::getAge;
Integer computerAge = getAge.apply(c1);
```

## 4. 方法引用

:: 操作符可以应用在如下场景

### 4.1 静态方法

```java
List inventory = Arrays.asList(
  new Computer( 2015, "white", 35), new Computer(2009, "black", 65));
inventory.forEach(ComputerUtils::repair);
```

### 4.2 现有对象的实例方法

使用 PrintStream 类型的 System.out 变量 调用 print 方法

```java
Computer c1 = new Computer(2015, "white");
Computer c2 = new Computer(2009, "black");
Computer c3 = new Computer(2014, "black");
Arrays.asList(c1, c2, c3).forEach(System.out::print);
```

### 4.3 特定类型任意变量实例方法

```java
Computer c1 = new Computer(2015, "white", 100);
Computer c2 = new MacbookPro(2009, "black", 100);
List inventory = Arrays.asList(c1, c2);
inventory.forEach(Computer::turnOnPc);
```

在这个例子中直接调用了 Computer 类的 turnOnPc，并没有实例化 Computer 对象 

### 4.4 特定对象的父类方法

在父类 Computer 中有这样的一个方法

```java
public Double calculateValue(Double initialValue) {
    return initialValue/1.50;
}
```

子类 MacBookPro 中重写这个方法

```java
@Override
public Double calculateValue(Double initialValue){
    Function<Double, Double> function = super::calculateValue;
    Double pcValue = function.apply(initialValue);
    return pcValue + (initialValue/10) ;
}
```

在 MacBookPro 实体调用

```java
macbookPro.calculateValue(999.99);
```

使用 supper :: calulateValue.apply 调用父类方法

## 5. 构造函数引用

### 5.1 创建一个新实例

引用构造函数实例化对象可以非常简单:

```java
@FunctionalInterface
public interface InterfaceComputer {
    Computer create();
}
 
InterfaceComputer c = Computer::new;
Computer computer = c.create();
```

如果有两个参数的构造函数

```java
BiFunction<Integer, String, Computer> c4Function = Computer::new; 
Computer c4 = c4Function.apply(2013, "white");
```

三个或以上必须重新定义函数接口

```java
@FunctionalInterface
interface TriFunction<A, B, C, R> { 
    R apply(A a, B b, C c); 
    default <V> TriFunction<A, B, C, V> andThen( Function<? super R, ? extends V> after) { 
        Objects.requireNonNull(after); 
        return (A a, B b, C c) -> after.apply(apply(a, b, c)); 
    } 
}

TriFunction <Integer, String, Integer, Computer> c6Function = Computer::new;
Computer c3 = c6Function.apply(2008, "black", 90);
```

### 5.2 创建一个数组

```java
Function <Integer, Computer[]> computerCreator = Computer[]::new;
Computer[] computerArray = computerCreator.apply(5);
```

[参考文章](https://www.baeldung.com/java-8-double-colon-operator)

