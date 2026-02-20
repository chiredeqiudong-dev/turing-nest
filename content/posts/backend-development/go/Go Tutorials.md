---
title: Go Tutorials
slug: go-tutorials
categories: 后端开发
tags:
  - Go
date: 2026-02-20
---

>Note：[官方教程](https://go.dev/doc/tutorial/)，操作系统：Linux，先看 [A Tour of Go](https://go.dev/tour/list) 效果会好一些，后续关于 Go 的最新消息可以看 [The Go Blog](https://go.dev/blog/)

官方为初学者提供的 Go 教程：

![](https://cdn.img.turingzy.cn/2026/202602201655571.png)

本篇将跟随官方的教程进行学习，并对每一章节进行总结。

## Install

[安装指南](https://go.dev/doc/install)

- 下载压缩包 & 解压
- 配置环境变量

```shell
$ go version
go version go1.25.7 linux/amd64
```

## Get started with Go

- 常用 Go 命令
- 层级划分：project、module、package
- [依赖管理 & 模块校验](https://go.dev/ref/mod#authenticating)

## Create a Go module

- 函数的声明

![](https://cdn.img.turingzy.cn/2026/202602201655575.png)

- 函数名称大写为导出（不同包可调用）
- 通过 `go mod edit -replace example.com/greetings=../greetings`，将模块重定向到本地目录
- 常见的错误处理方式：将 `error` 作为值返回，以便调用者可以检查它
- 切片（slice），一个数据结构类似动态数组，切片的声明不需要指定长度，否则是定长数组
- 哈希表的声明：`make(map[key-type]value-type)`
- 单元测试，编写测试函数`func TestName(t *testing.T) {}`，文件名称以 `_test.go` 命令
- `go build & go install`（编译 & 安装）

## Getting started with multi-module workspaces

- 解决本地多模块同时开发时的依赖问题，替代 `go.mod` 中的 `replace` 指令
- 在工作区执行 `go work init` 初始化，并通过 `go work use dir` 挂载本地模块

## Accessing a relational database

- 连接关系型数据库：[数据库驱动包](https://go.dev/wiki/SQLDrivers)
- 全局变量、结构体的声明

```go
type album struct {
    ID     string  `json:"id"`
    Title  string  `json:"title"`
    Artist string  `json:"artist"`
    Price  float64 `json:"price"`
}
```

- `defer` 关键字：“压栈延迟执行” + “函数退出前自动弹出执行”的机制，Go 里最推荐的资源清理方式
- 多行、单行、插入等数据库操作

## Developing a RESTful API with Go and Gin

- 使用 [Gin](https://gin-gonic.com/) 作 Web 框架，进行基础  RESTful API 开发

## Getting started with generics

- 通过单个函数兼容多种类型，消除冗余代码
- 泛型核心语法与概念：类型参数、类型约束、自定义类型约束
- 泛型函数的调用方式：显式指定类型参数、隐式类型推断（编译器行为）
- `comparable` 关键字：Go 为泛型设计的**内置类型约束**，限定类型参数必须支持 `==` 和 `!=` 比较操作
- 类型参数必须支持泛型函数内的所有操作

```go
// 自定义类型约束
type Number interface{ int64 | float64 }

// 泛型函数（类型参数+约束）
func SumMap[K comparable, V Number](m map[K]V) V {
	var sum V
	for _, v := range m {
		sum += v
	}
	return sum
}

func main() {
	m := map[string]int64{"a": 10, "b": 20}
	// 显式指定类型参数
	s1 := SumMap[string, int64](m)
	// 隐式类型推断
	s2 := SumMap(m)
	fmt.Printf("显式：%d  隐式：%d\n", s1, s2)
}
```

## Getting started with fuzzing

-  [Go Fuzzing glossatry（模糊测试术语表）](https://go.dev/doc/security/fuzz/#glossary)

模糊测试示例：

![](https://cdn.img.turingzy.cn/2026/202602201655577.png)

- 单元测试存在局限性，即每个输入都必须由开发者添加到测试中
- 模糊测试的一个好处是它会为你的代码生成输入，并可能识别出你编写的测试用例未能覆盖的边界情况
- `func FuzzName(f *testing.F) {}`
- ` go test -fuzz=Fuzz` 运行模糊测试。如果模糊测试过程中发生了错误，导致问题的输入被写入一个种子语料库文件（testdata/fuzz/FuzzReverse/b3a02a82d7faf0f8），下次调用 `go test` 时会运行该文件，即使没有 `-fuzz` 标志
- 一个经典的字节、字符反转问题

## Find and fix vulnerable dependencies with govulncheck

- 安装：`go install golang.org/x/vuln/cmd/govulncheck@latest`
- 分析依赖是否存在漏洞：`govulncheck ./...`
- 分析漏洞、升/降级依赖版本，然后再次检查

## Find and fix vulnerable dependencies with VS Code Go

- 使用 VSCode 通过命令 `Toggle VulnCheck` 检查依赖漏洞

## A Tour of Go

>Note：[官方指南](https://tour.go-zh.org/list)，基本语法和数据结构；方法和接口；以及 Go 的并发原语

### Basics

#### Package、Variable And Function

>Note：[Go 的声明语法](https://blog.go-zh.org/gos-declaration-syntax)

Go 的基本类型

```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

float32 float64

complex64 complex128 // 复数

byte // uint8 的别名

rune // int32 的别名，表示一个 Unicode 码位
```

- `int`、`uint` 和 `uintptr` 类型在 32-位系统上通常为 32-位宽，在 64-位系统上则为 64-位宽
- 变量 `var`，常量 `const`（常量不能用 `:=` 语法声明）
- 与 C 不同的是，Go 在不同类型的项之间赋值时需要**显式转换**
- 声明一个变量而不指定其类型时（即使用不带类型的 `:=` 语法），变量的类型会通过右值推断出来
- 如果右值是数值常量，那么变量类型取决于常量的精度，并且一个未指定类型的常量由上下文来决定其类型

#### Flow Control Statements

- 注意变量的作用域
- 只有一种循环结构：`for` 循环
- `for` 关键字后面的三个构成部分没有小括号， 大括号 `{ }` 是必须的（`if`、`switch` 同理）
- 初始化语句和后置语句是可选的，相当于只有条件表达式（类似 `while`）
- `if`、`switch` 语句可以在条件表达式前执行一个简短语句（比如变量初始化）
- `switch` 默认不隐式穿透，每个 case 自带 `break`，可以通过 `fallthrough` 强制穿透
- `switch` 甚至可以没有条件，相比 `if-then-else` 可以写得更加清晰
- [defer](https://blog.go-zh.org/defer-panic-and-recover) 推迟调用的函数调用会被压入一个栈中。 当外层函数返回时，被推迟的调用会按照后进先出的顺序调用

#### Structs、Slices and Maps

>Note：[切片的用法和本质](https://blog.go-zh.org/go-slices-usage-and-internals)

- Go 拥有指针，指针保存了值的**内存地址**。类型 `*T` 是指向 `T` 类型值的指针，其零值为 `nil`；`&` 操作符会生成一个指向其操作数的指针；`*` 操作符表示指针指向的底层值；Go 没有指针运算

```plaintext
		变量名        内存地址 (假设)       存储的值
      +-------+      +------------+      +---------+
      |   i   | ---> | 0x14000010 | ---> |   42    |  <--- 当你调用 *p 时，读取的就是这个值
      +-------+      +------------+      +---------+
                                              ^
                                              | 指向底层值
                                              |
      +-------+      +------------+      +---------+
      |   p   | ---> | 0x14000018 | ---> |0x14000010|  <--- p 保存的是 i 的内存地址 (&i)
      +-------+      +------------+      +---------+
```

- 一个 结构体（`struct`）就是一组 字段（field）
- 对于结构体的指针，Go 允许隐式解引用 `(*p).X -> p.X`

- 类型 `[n]T` 表示一个数组，它拥有 `n` 个类型为 `T` 的值；数组的长度是其类型的一部分，因此数组不能改变大小
- 类型 `[]T` 表示一个元素类型为 `T` 的切片；切片则为数组元素提供了动态大小、灵活的视角
- 切片通过两个下标来界定，一个下界和一个上界，二者以冒号分隔（遵循**左闭右开**原则，下界的默认值为 0，上界则是该切片的长度）
- 切片就像数组的引用，切片并不存储任何数据，它只是描述了**底层数组**中的一段

```plaintext
切片头 (Slice Header)                         底层数组 (Underlying Array)
+-----------------+                          0      1      2      3      4
| ptr: *string    | ----------------------> +------+------+------+------+------+
+-----------------+                         |  "A" |  "B" |  "C" |  "D" |  "E" |
| len: 3          |                         +------+------+------+------+------+
+-----------------+                                  ^                ^
| cap: 4          |                                  |                |
+-----------------+                                  +----------------+
                                                        切片当前长度 (len=3)
                                                     |                         |
                                                     +-------------------------+
                                                        切片最大容量 (cap=4)
```

- 切片的长度就是它所包含的元素个数，容量是从它的第一个元素开始数，到其底层数组元素末尾的个数
- 切片可以用内置函数 `make([]T, len, cap)` 来创建，`make()` 会分配一个元素为零值的数组，容量参数可选
- 为切片追加新的元素使用`func append(s []T, value ...T) []T`，当 `s` 的底层数组太小，不足以容纳所有给定的值时，它就会分配一个更大的数组
- `for` 循环的 `range` 形式可遍历切片或哈希表，每次迭代都会返回两个值，第一个值为当前元素的下标，第二个值为该下标所对应元素的一份**副本**

```go
for i, v := range slices {...}

// 对于不需要的值可用 _ 忽略
for _, v := range slices {...}
for i := range slices {...}
```

- `map[key-type]value-type` 哈希表的零值为 `nil` 。`nil` 哈希表既没有键，也不能添加键
- 删除元素 `delete(m, key)`
- 判断 Key 是否存在：`if elem, ok := m[key]; ok {...}`

- **函数也是值**，它们可以像其他值一样传递，比如用作其他函数的参数或返回值
- 函数闭包，$闭包 = 函数代码 + 它捕获的外部变量$

```go
package main

import "fmt"

// makeCounter 是一个“外部函数”，它的返回值是一个函数 func() int
func makeCounter() func() int {
	count := 0
	
	// 定义并返回一个匿名函数（内部函数）
	// 这个内部函数“引用”了它外部的变量 count
	counterFunc := func() int {
		count++ // 它可以修改外部的 count
		return count
	}

	return counterFunc
}

func main() {
	// c1 得到的就是那个背着“count变量”背包的匿名函数
	c1 := makeCounter()

	fmt.Println("c1 第一次调用:", c1()) // 输出 1
	fmt.Println("c1 第二次调用:", c1()) // 输出 2
	// 你看，它“记住”了上次 count 的值是 1，这次变成了 2

	fmt.Println("-----------------------")

	// 创建第二个计数器实例，全新的环境
	c2 := makeCounter()
	fmt.Println("c2 第一次调用:", c2()) // 输出 1。c2 有它自己独立的 count 变量
	fmt.Println("c1 第三次调用:", c1()) // 输出 3。c1 的状态不受影响
}
```

### Methods and Interfaces

- Go 没有类，不过你可以为类型定义方法，方法就是一类带特殊的**接收者**参数的**函数**
- 接收者的类型定义和方法声明必须在同一包内
- 接收者分为指针类型和值类型，对于值类型而言方法操作的是其副本，所以修改接收者的值，需要使用指针类型
- 接收者为指针的的方法被调用时，接收者既能是值又能是指针：`var v Vertex; v.Scale(5) -> (&v).Scale(5)`
- 以值为接收者的方法被调用时，接收者既能为值又能为指针：`p := &v; p.Abs() -> (*p).Abs()`
- 使用指针接收者的原因有二：
	1. 方法能够修改其接收者指向的值
	2. 可以避免在每次调用方法时复制该值。若值的类型为大型结构体时，这样会更加高效
- 所有给定类型的方法都应该有值或指针接收者，因为：

```go
type Vertex struct {
	X, Y float64
}

// 值类型
func (v Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

// 指针类型
func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```

- **接口类型**定义了一组方法签名；任何实现了这些方法的值，都可以赋值给该接口类型的变量
- 类型通过实现一个接口的所有方法来实现该接口，无需显式声明，没有 `implements` 关键字
- 接口也是值。它们可以像其它值一样传递；在内部，接口值可以看做包含值和具体类型的元组 `(value, type)`
- 由于接口变量本身并不为 `nil`，所以即使它底层的具体值为 `nil`，我们依然可以正常调用方法（此时方法会接收到一个 `nil` 接收者）
- 调用 `nil` 接口的方法会导致运行时 panic，因为接口底层缺少用于方法动态派发的类型信息
- 空接口 `interface{}` 可保存任何类型的值（每个类型都至少实现了零个方法）

- **类型断言**可以用来获取接口底层的具体值 `t := i.(T)`。若 `i` 并未保存 `T` 类型的值，触发 `panic`。`t, ok := i.(T)`，`ok` 将为 `false` 而 `t` 将为 `T` 类型的零值，程序并不会产生 `panic`
- **类型选择**本质上是多重类型断言的集合。它类似于常规的 `switch` 语句，但 `case` 用于判断**类型**而非值

- Go 程序使用 `error` 值来表示错误状态，`error` 类型是一个内建接口

```go
type error interface {
    Error() string
}
```

- 函数通常会返回一个 `error` 值。调用方必须检查该错误是否为 `nil`，以此决定后续的处理逻辑

### Generics

- 可以使用**类型参数**编写 Go 函数来处理多种类型。 类型参数出现在函数参数之前的方括号里

```go
func Index[T comparable](s []T, x T) int
```

- `s` 是类型为 `T` 的切片，且 `T` 满足内置约束 `comparable`；变量 `x` 同样是 `T` 类型
- `comparable` 约束保证了我们可以使用 `==` 和 `!=` 来对比该类型的值
- Go 不仅支持泛型函数，还支持**泛型类型**。通过引入类型参数，我们可以非常便捷地构建通用的数据结构

```go
// List 表示一个可以保存任何类型的值的单链表。
type List[T any] struct {
	next *List[T]
	val  T
}

// 底层支持
type any = interface{}
```

### Concurrency

- Goroutine（协程）就是 Go 语言自己管理的**轻量级线程**。
- 线程归操作系统管，协程归 Go 程序自己管；多个协程可以复用（跑在）少数几个真实的物理线程上
- 协程的三大优势：
	1. 内存初始分配极小，只有几 KB（按需分配，自动扩容）
	2. 创建和销毁不需要“跨界”（用户态）
	3. 上下文切换成本极低，只需要保存最关键的 **3 个寄存器**（PC 程序计数器、SP 栈指针、BP 基址指针）
- Channel 是 Go 语言里一条带有强类型约束且并发安全的 “数据传送带”，Goroutine 们借由它不仅能安全地互传数据，还能自然而然地完成彼此间的同步与等待
- Channel 创建方式：`ch := make(chan T)`，默认情况下，发送和接收操作在另一端准备好之前都会阻塞
- Channel 可以是**带缓冲的**，比如：`ch := make(chan int, 100)`，仅当 Channel 的缓冲区填满后，向其发送数据时才会阻塞；当缓冲区为空时，接受方会阻塞

![](https://cdn.img.turingzy.cn/2026/202602201655578.png)

>Note：来源 [dev.to](https://dev.to/souvikinator/understanding-goroutines-and-channels-in-golang-with-intuitive-visuals-16gj)

- 发送者可通过 `close` 关闭一个 Channel 来表示没有需要发送的值，接收者可以通过为接收表达式分配第二个参数来判断信道是否被关闭 `v, ok := <-ch`
- 循环 `for i := range c` 会不断从 Channel 接收值，直到它被关闭
- 只应由发送者关闭 Channel，而不应由接收者关闭。向一个已经关闭的 Channel 发送数据会引发程序 panic
- `select` 就是 Go 里的 “多路监听器”。可以把它完全当做是专门给 Channel 定制的 `switch` 语句

```Plaintext
[电话 A: 外卖通道] ----\
                        \
[电话 B: 快递通道] ---->  【 select 监控中心 (你) 】 ---> 哪部先响，接哪部
                        /  (都不响，就死等)             (同时响，随机接)
[电话 C: 投诉通道] ----/
```

- 当 `select` 语句中所有的 Channel 操作都处于被阻塞 (Blocked) 状态时，`default` 分支就会执行

- Go 标准库中提供了 [sync.Mutex](https://go-zh.org/pkg/sync/#Mutex) 互斥锁类型及其两个方法，通过在代码前调用 `Lock()`，在代码后调用 `Unlock()` 来保证一段代码的互斥执行