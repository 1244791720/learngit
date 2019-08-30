# Go 笔记

## 基础

### for 循环

#### 1、标准 for 循环

```go
for initialization; condition; post {
// zero or more statements
}
```

- initialization ：循环开始前执行。initalization 如果存在，必须是一条简单语句
  （simple statement），即，短变量声明、自增语句、赋值语句或函数调用。

- condition ：condition  是一个布尔表达式（boolean expression），其值在每次循环迭代开始时计算。如果为 true  则执行循环体语句。

- post：post  语句在循环体执行结束后执行，之后再次对 conditon  求
  值。 condition  值为 false  时，循环结束。

for 循环这3部分都可省略，全省略时即为死循环。也可以通过 break 或 return结束循环。

#### 2、for range

```go
for index, value := range iterative {
// zero or more statements
}
```

每次循环迭代， range  产生一对值；索引以及在该索引处的元素值。这个例子不需要索引，但 range  的语法要求, 要处理元素, 必须处理索引。一种思路是把索引赋值给一个临时变量,如 temp  , 然后忽略它的值，但Go语言不允许使用无用的局部变量（local variables），因为这会导致编译错误。

Go语言中这种情况的解决方法是用 空标识符  （blank identifier），即 _  （也就是下划线）。Go语言中这种情况的解决方法是用 空标识符  （blank identifier），即 _  （也就是下划线）。

### 变量声明的几种方式及使用场景

声明一个变量有好几种方式，下面这些都等价：

- s := ""

- var s string

- var s = ""

- var s string = ""

第一种形式，是一条短变量声明，最简洁，但只能用在函数内部，而不能用于包变量。第二种形式依赖于字符串的默认初始化零值机制，被初始化为""。第三种形式用得很少，除非同时声明多个变量。第四种形式显式地标明变量的类型，当变量类型与初值类型相同时，类型冗余，但如果两者类型不同，变量类型就必须了。实践中一般使
用前两种形式中的某个，初始值重要的话就显式地指定变量的类型，否则使用隐式初始化。



### 内置函数 make

make 函数定义

```go
func make(t Type, size ...IntegerType) Type
```

#### 1、make slice

```go
slices := make([]string, 1, 3)
```

对于 Slice：

size指定Slice的长度。Slice的容量也等于它的长度。可以向其提供第二个整形参数指定不同的容量，第二参数必须不小于它的长度。

#### 2、make map

```go
myMap := make(map[string]string)
```

对于 map：

在所需容量较小时可以不设置 size， go 会分配足够的空间来容纳。当 map 增长到容量上限时 map 大小自动加 1，因此对于大 map 或快速扩张的情况最好先设置合适的 size。类似  Java 集合类在初始化时最好指定大小。

#### 3、make chanel

```go
myChan := make(chan <- string, 10)
```

对于 chanel：

chanel根据指定的缓存容量初始化，如果为零，或者省略了大小，则通道为无缓冲的。

