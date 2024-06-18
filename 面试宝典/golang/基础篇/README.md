# 1.1 Go 中的 = 和 := 有什么区别？

在对一个变量进行赋值前，首先要声明其类型。

```go
// 声明
var age int

// 赋值
age = 18
```

而这两行代码其实可以使用 `:=` 来合并成一行代码

```go
age := 18
```

因此它们的区别是

-   `=` 是赋值
-   `:=` 是声明并赋值

一个变量只能声明一次，使用多次 `:=` 是不允许的，而当你声明一次后，却可以赋值多次，没有限制。

# 1.2 Go 中的指针的意义是什么？

## 什么是指针和指针变量

普通的变量，存储的是数据，而指针变量，存储的是数据的内存地址。

学习指针，主要有两个运算符号，要记牢

-   `&`：地址运算符，从变量中取得值的内存地址

```go
// 定义普通变量并打印
age := 18
fmt.Println(age) //output: 18

ptr := &age
fmt.Println(ptr) //output: 
```

-   `*`：解引用运算符，从内存地址中取得存储的数据

```go
myage := *ptr
fmt.Println(myage) //output: 18
```

## 指针的意义是什么？

**意义一：省内存**

当你往一个函数传递参数时，若该参数是一个值类型的变量，则在调用函数时，会将原来的变量的值拷贝一遍。

假想每次传参都用数组，那么每次数组都要被复制一遍。如果数组大小有 100万，在64位机器上就需要花费大约 800W 字节，即 8MB 内存。这样会消耗掉大量的内存。

**意义二：易编码**

写了一个函数来实现更新某对象里的一些数据，在值类型的变量中，若不使用指针，则函数需要重新返回一个更新过的全新对象。

而有了指针，则可以不用返回。

# 1.3 Go 多值返回有什么用？

Go语言中函数可以返回多个值，这和其它编程语言有很大的不同。对于有其它语言编程经验的人来说，最大的障碍不是学习这个特性，而是很难想到去使用这个特性。

利用这个特性，在 Go 中实现变量的交换，就不需要再使用中间变量（表象上看是这样，但实际还是会变量的拷贝）了，非常的方便。

以下是使用示例

```go
package main

import "fmt"

func swap(a int, b int) (int, int) {
	return b, a

}

func main() {
	a := 1
	b := 2

	a, b = swap(a, b)

	fmt.Println(a) // 2
	fmt.Println(b) // 1
}
```

若返回的值，有的不需要，可以直接使用 占位符  `_` 接收，表示丢弃这个值。

```go
a, _ = swap(a, b)
```

在 Go 中没有异常机制，当一个函数运行出错的时候，除了返回该功能函数的结果外，还应该返回一个 error 类型的值。

若该值为 nil 则表示，函数正常运行结束，反之，则函数运行异常。

这是 Golang 这门语言的设计哲学，因此我们不管在看别人的代码，亦或者自己写代码，都会发现代码中到处都有下面这段代码的身影。

```go
if err != nil {
  // handle error
} 
```

# 1.4 Go 有异常类型吗？

在解答这个问题前，有必要对错误和异常做一个解释

-   **错误**：指的是可能出现问题的地方出现了问题，比如打开一个文件时失败，这种情况在人们的意料之中 ；
-   **异常**：指的是不应该出现问题的地方出现了问题，比如引用了空指针，这种情况在人们的意料之外。

**在 Go 没有异常类型，只有错误类型（Error）。**

一个函数要是想返回错误，通常会使用返回值来表示异常状态，它很像 C语言中的错误码，可逐层返回，直到被处理。

```go
f, err := os.Open("test.txt")
if err != nil {
    log.Fatal(err)
}
```

Go 语言中虽然没有异常的概念，但是却有更为恐怖的 panic ，由于有了 recover，在一定程度上， panic 可以类比做异常。

Golang错误和异常（panic）是可以互相转换的：

1.  **错误转异常**：比如程序逻辑上尝试请求某个URL，最多尝试三次，尝试三次的过程中请求失败是错误，尝试完第三次还不成功的话，失败就被提升为异常了。
2.  **异常转错误**：比如panic触发的异常被recover恢复后，将返回值中error类型的变量进行赋值，以便上层函数继续走错误处理流程。
#  1.5 Go 中的 rune 和 byte 有什么区别？

一个字符串是由若干个字符组合而成的，比如 `hello`，就由 5 个字符组成。

在 Go 中字符类型有两种，分别是：

1.   byte 类型：字节，是 uint8 的别名类型
2.   rune 类型：字符，是 int32 的别名类型

byte 和 rune ，虽然都能表示一个字符，但 byte 只能表示 ASCII 码表中的一个字符（ASCII 码表总共有 256 个字符），数量远远不如 rune 多。

rune 表示的是 Unicode字符中的任一字符，而我们都知道，Unicode 是一个可以表示世界范围内的绝大部分字符的编码，这张表里几乎包含了全世界的所有的字符，当然中文也不在话下。

能表示的字符更多，意味着它占用的空间，也要更大，所占空间是 4个 byte 的大小。

下面以一段代码来验证一下他们的占用空间的差异

```go
var a byte = 'A'
var b rune = 'B'
fmt.Printf("a 占用 %d 个字节数\n", unsafe.Sizeof(a))
fmt.Printf("b 占用 %d 个字节数\n",unsafe.Sizeof(b))

// output
a 占用 1 个字节数
b 占用 4 个字节数
```

# 1.6 Go 语言中的深拷贝和浅拷贝？

## 什么是拷贝？

当你把 a 变量赋值给 b 变量时，其实就是把 a 变量拷贝给 b 变量

```go
a := "hello"
b := a
```

这只是拷贝最简单的一种形式，而有些形式却表现得非常的隐蔽。比如：

-   你往一个函数中传参
-   你向通道中传入对象

这些其实在 Go编译器中都会进行拷贝的动作。

## 什么是深浅拷贝？

知道了什么是拷贝，那我们再往深点开挖，聊聊深浅拷贝。

不过先别急，咱先了解下数据结构的两种类型：

-   **值类型** ：String，Array，Int，Struct，Float，Bool

-   **引用类型**：Slice，Map

这两种不同的类型在拷贝的时候，在拷贝的时候效果是完全不一样的，这对于很多新手可能是一个坑。

对于值类型来说，你的每一次拷贝，Go 都会新申请一块内存空间，来存储它的值，改变其中一个变量，并不会影响另一个变量。

```go
func main() {
	aArr := [3]int{0,1,2}
	fmt.Printf("打印 aArr: %v \n", aArr)
	bArr := aArr
	aArr[0] = 88
	fmt.Println("将 aArr 拷贝给 bArr 后，并修改 aArr[0] = 88")
	fmt.Printf("打印 aArr: %v \n", aArr)
	fmt.Printf("打印 bArr: %v \n", bArr)
}
```

从输出结果来看，aArr 和 bArr 相互独立，互不干扰

```go
打印 aArr: [0 1 2] 
将 aArr 拷贝给 bArr 后，并修改 aArr[0] = 88
打印 aArr: [88 1 2] 
打印 bArr: [0 1 2] 
```

对于引用类型来说，你的每一次拷贝，Go 不会申请新的内存空间，而是使用它的指针，两个变量名其实都指向同一块内存空间，改变其中一个变量，会直接影响另一个变量。

```go
func main() {
	aslice := []int{0,1,2}
	fmt.Printf("打印 aslice: %v \n", aslice)
	bslice := aslice
	aslice[0] = 88
	fmt.Println("将 aslice 拷贝给 bslice 后，并修改 aslice[0] = 88")
	fmt.Printf("打印 aslice: %v \n", aslice)
	fmt.Printf("打印 bslice: %v \n", bslice)
}
```

从输出结果来看，aslice 的更新直接反映到了 bslice 的值。

```go
打印 aslice: [0 1 2] 
将 aslice 拷贝给 bslice 后，并修改 aslice[0] = 88
打印 aslice: [88 1 2] 
打印 bslice: [88 1 2] 
```


# 1.7 什么叫字面量和组合字面量？

说出来不怕大家笑话，在去年年初我刚开始学习 Go 基础的时候，有一个词困扰了我好久，这个词就是 **字面量**。

之所以会让我理解困难，是因为在 Go 之前，我都是写 Python 的，而且写了很多年，在 Python 中万物皆对象，不管一个字面量）有没有变量名来承接，在使用上没有任何区别的，因此在学 Go 之前，我其实都不知道有字面量这么个概念。

```python
>>> {"name": "iswbm"}.get("name")  # 使用字面量
'iswbm'
>>>
>>> profile={"name": "iswbm"}   # 使用变量名
>>> profile.get("name")
'iswbm'
```

那么字面量到底是啥东西？怎么那么多的基础教程里反复会提及，却好像没什么人把这个名词的概念解释一下呢？难道是因为这是常识？尴尬。。

![](http://image.iswbm.com/20211008234746.png)

相信正在看这篇文章的你，可能也会有此疑问，今天我就梳理一下，我理解中的 **字面量**，是什么意思？它与普通变量有什么区别？

## 1. 什么是字面量？

在 Go 中内置的基本类型有：

-   布尔类型：`bool`

-   11个内置的整数数字类型：`int8`, `uint8`, `int16`, `uint16`, `int32`, `uint32`, `int64`, `uint64`, `int`, `uint`和`uintptr`

-   浮点数类型：`float32`和`float64`

-   复数类型：`complex64`和`complex128`

-   字符串类型：`string`

而这些基本类型值的文本，就是基本类型字面量。

比如下面这两个字符串，都是字符串字面量，没有用变量名或者常量名来指向这两个字面量，因此也称之为 **未命名常量**。

```go
"hello, iswbm"

`hello,
iswbm`
```

## 2. 同值不同字面量

值的字面量（literal）是代码中值的文字表示，一个值可能存在多种字面量表示。

举个例子，十进制的数值 15，可以由三种字面量表示

```go
// 16进制
0xF

// 8进制
0o17

// 2进制
0b1111
```

通过比较，可以看出他们是相等的

```go
import "fmt"

func main() {
	fmt.Println(15 == 0xF)     // true
	fmt.Println(15 == 017)     // true
	fmt.Println(15 == 0b1111)  // true
}
```

## 3. 字面量和变量有啥区别？

下面这是一段很正常的代码

```go
func foo() string {
	return "hello"
}

func main() {
	bar := foo()
	fmt.Println(&bar)
}
```

可要是换成下面这样

```go
func foo() string {
	return "hello"
}

func main() {
	fmt.Println(&foo())
}
```

可实际上这段代码是有问题的，运行后会报错

```
./demo.go:11:14: cannot take the address of foo()
```

你一定觉得很奇怪吧？

为什么先用变量名承接一下再取地址就不会报错，而直接使用在函数返回后的值上取地址就不行呢？

这是因为，如果不使用一个变量名承接一下，函数返回的是一个字符串的文本值，也就是字符串字面量，而这种基本类型的字面量是不可寻址的。

要想使用 `&` 进行寻址，就必须得用变量名承接一下。

## 4. 什么是组合字面量？

首先看下Go文档中对组合字面量（Composite Literal）的定义：

>   Composite literals construct values for structs, arrays, slices, and maps and create a new value each time they are evaluated. They consist of the type of the literal followed by a brace-bound list of elements. Each element may optionally be preceded by a corresponding key。

翻译成中文大致如下： 组合字面量是为结构体、数组、切片和map构造值，并且每次都会创建新值。它们由字面量的类型后紧跟大括号及元素列表。每个元素前面可以选择性的带一个相关key。

**什么意思呢？所谓的组合字面量其实就是把对象的定义和初始化放在一起了**。

接下来让我们看看结构体、数组、切片和map各自的常规方式和组合字面量方式。

### 结构体的定义和初始化

让我们看一个struct结构体的常规的定义和初始化是怎么样的。

**常规方式**

常规方式这样定义是逐一字段赋值，这样就比较繁琐。

```golang
type Profile struct {
	Name string
	Age int
	Gender string
}

func main() {
	// 声明对象
	var xm Profile
	
	// 属性赋值
	xm.Name = "iswbm"
	xm.Age = 18
	xm.Gender = "male"
}
```

**组合字面量方式**

```golang
type Profile struct {
	Name string
	Age int
	Gender string
}

func main() {
	// 声明 + 属性赋值
	xm := Profile{
		Name:   "iswbm",
		Age:    18,
		Gender: "male",
	}
}
```

### 数组的定义和初始化

**常规方式**

在下面的代码中，我们在第1行定义了一个8个元素大小的字符串数组。然后一个一个的给元素赋值。即数组变量的定义和初始化是分开的。

```golang
var planets [8]string

planets[0] = "Mercury" //水星
planets[1] = "Venus" //金星
planets[2] = "Earth" //地球
```

**组合字面量方式**

该示例中，就是将变量balls的定义和初始化合并了在一起。

```golang
balls := [4]string{"basketball", "football", "Volleyball", "Tennis"}
```

### slice的定义和初始化

**常规方式**

```golang
// 第一种
var s []string //定义切片变量s，s为默认零值nil
s = append(s, "hat", "shirt") //往s中增加元素，len(s):2,cap(s):2

// 第二种
s := make([]string, 0, 10) //定义s，s的默认值不为零值
```

**组合字面量方式**

由上面的常规方式可知，首先都是需要先定义切片，然后再往切片中添加元素。接下来我们看下组合字面量方式。

```golang
s := []string{"hat", "shirt"} //定义和初始化一步完成，自动计算切片的容量和长度
// or
var s = []string{"hat", "shirt"}
```

### map的定义和初始化

**常规方式**

```golang
//通过make函数初始化
m := make(map[string]int, 10)
m["english"] = 99
m["math"] = 98
```

**组合字面量方式**

```golang
m := map[string]int {
	"english": 99,
	"math": 98,
}

//组合字面量初始化多维map
m2 := map[string]map[int]string {
	"english": {
		10: "english",
	},
}
```

显然，使用组合字面量会比常规方式简单了不少。

## 5. 字面量的寻址问题

字面量，说白了就是未命名的常量，跟常量一样，他是不可寻址的。

这边以数组字面量为例进行说明

```go
func foo() [3]int {
	return [3]int{1, 2, 3}
}

func main() {
	fmt.Println(&foo())
	// cannot take the address of foo()
}
```

关于寻址性的内容，你可以在我的另一篇文章中（[1.15 Go中哪些是可寻址，哪些是不可寻址的？](https://go-interview.iswbm.com/c01/c01_15.html)）进行学习，总结得非常详细。

# 1.8 对象选择器自动解引用怎么用？

从一个结构体实例对象中获取字段的值，通常都是使用 `.` 这个操作符，该操作符叫做 **选择器**。

选择器有一个妙用，可能大多数人都不清楚。

当你对象是结构体对象的指针时，你想要获取字段属性时，按照常规理解应该这么做

```go
type Profile struct {
	Name string
}

func main() {
	p1 := &Profile{"iswbm"}
  fmt.Println((*p1).Name)  // output: iswbm
}
```

但还有一个更简洁的做法，可以直接省去 `*` 取值的操作，选择器 `.` 会直接解引用，示例如下

```go
type Profile struct {
	Name string
}

func main() {
	p1 := &Profile{"iswbm"}
	fmt.Println(p1.Name)  // output: iswbm
}
```

也正是这个原因，因此在给你一个方法指定定一个接收者的时候，访问接收者的对象时，不需要像下面这样显示的解引用

```go
type Person struct {
	name string
}

func (p *Person) Say() {
	fmt.Println((*p).name)
}
```

而可以直接这样写

```go
type Person struct {
	name string
}

func (p *Person) Say() {
	fmt.Println(p.name)
}
```

# 1.9 map 的值不可寻址，那如何修改值的属性？

要回答本题，需要你知道什么是不可寻址。

不急，请先看一下如下这段代码，你知道它有什么问题吗？

```go
package main

type Person struct {
    Age int
}

func (p *Person) GrowUp() {
    p.Age++
}

func main() {
    m := map[string]Person{
        "iswbm": Person{Age: 20},
    }
    m["iswbm"].Age = 23
    m["iswbm"].GrowUp()
}
```

没错，这段代码是错误的，当你编译时，会直接报错呢？

原因在于这两行

```go
    m["iswbm"].Age = 23
    m["iswbm"].GrowUp()
```

我们知道 map 的值是不可寻址的，当你使用 ` m["zhangsan"]` 取得值时，其实返回的是其值的拷贝，虽然与原数据值相同，但是在内存中并不是同一个数据。

也正是这样，当 map 的值是一个普通对象（非指针），是无法直接对其修改的。

针对这种错误，解决方法有两种：

## 第一种：新建变量，修改后再覆盖

```go
func main() {
	m := map[string]Person{
		"iswbm": Person{Age: 20},
	}
	p := m["iswbm"]
	p.Age = 23
	p.GrowUp()
	m["iswbm"] = p
}
```

## 第二种：使用指针的方式

```go
func main() {
	m := map[string]*Person{
		"iswbm": &Person{Age: 20},
	}
	m["iswbm"].Age = 23
	m["iswbm"].GrowUp()
}
```

# 1.10 有类型常量和无类型常量的区别？

在 Go 语言中，常量分为有类型常量和无类型常量。

```go
// 有类型常量
const VERSION string = "v1.0.0"

// 无类型常量
const RELEASE = 3
```

那么他们有什么区别呢？

当你把有无类型的常量，赋值给一个变量的时候，无类型的常量会被隐式的转化成对应的类型

```go
package main

import "fmt"


func main() {
	const RELEASE = 3

	var x int16 = RELEASE
	var y int32 = RELEASE
	fmt.Printf("type: %T \n", x) //type: int16
	fmt.Printf("type: %T \n", y) //type: int32 
}
```

可要是有类型常量，不就会进行转换，在赋值的时候，类型检查就不会通过，从而直接报错

```go
package main

import "fmt"


func main() {
	const RELEASE int8 = 3

	var x int16 = RELEASE //cannot use RELEASE (type int8) as type int16 in assignment
	var y int32 = RELEASE //cannot use RELEASE (type int8) as type int32 in assignment
	fmt.Printf("type: %T \n", x) 
	fmt.Printf("type: %T \n", y) 
}
```

解决的方法是进行显式的转换

```go
package main

import "fmt"


func main() {
	const RELEASE int8 = 3

	var x int16 = int16(RELEASE) 
	var y int32 = int32(RELEASE) 
	fmt.Printf("type: %T \n", x)  // type: int16
	fmt.Printf("type: %T \n", y)  // type: int32
}
```

# 1.11 为什么传参使用切片而不使用数组？

**Go里面的数组是值类型，切片是引用类型。**

**值类型**的对象在做为实参传给函数时，形参是实参的另外拷贝的一份数据，对形参的修改不会影响函数外实参的值。

因此在如下例子中两次打印的指针地址是不一样的

```go
package main

import "fmt"

func arrayTest (x [2]int) {
	fmt.Printf("%p \n", &x)  // 0xc0000b4030 
}

func main() {
	arrayA := [2]int{1,2}
	fmt.Printf("%p \n", &arrayA) // 0xc0000b4010 
	arrayTest(arrayA)
}
```

假想每次传参都用数组，那么每次数组都要被复制一遍。如果数组大小有 100万，在64位机器上就需要花费大约 800W 字节，即 8MB 内存。这样会消耗掉大量的内存。

而**引用类型**，则没有这个拷贝的过程，实参与形参指向的是同一块内存地址

```go
package main

import "fmt"

func sliceTest (x []int) {
	fmt.Printf("%p \n", x)
}

func main() {
	sliceA := make([]int, 0)
	fmt.Printf("%p \n", sliceA)
	sliceTest(sliceA)
}
```

由此我们可以得出结论：

把第一个大数组传递给函数会消耗很多内存，采用切片的方式传参可以避免上述问题。切片是引用传递，所以它们不需要使用额外的内存并且比使用数组更有效率。

那么你肯定要问了，数组指针也是引用类型啊，也不一定要用切片吧？

确实，传递数组指针是可以避免对值进行拷贝的内存浪费。



# 1.12 Go 语言中 hot path 有什么用呢？

hot path ，热点路径，顾名思义，是你的程序中那些会频繁执行到的代码。

对于这些代码，由于执行次数非常多，意味着只要有一点设计或编码问题，影响就会被不断放大，而相反，你只要在这些代码中做一些优化，带来的效果也是非常明显的。

hot path 只是一个概念，到底你的程序中有哪些 hot path 还需要根据实际情况分析。

这边举一个比较常见的 hot path 优化的例子，在 `sync.Once` 有这么一段代码

在注释中首次提到了 `hot path` 概念

```go
// src/sync/once.go 

// Once is an object that will perform exactly one action.
//
// A Once must not be copied after first use.
type Once struct {
	// done indicates whether the action has been performed.
	// It is first in the struct because it is used in the hot path.
	// The hot path is inlined at every call site.
	// Placing done first allows more compact instructions on some architectures (amd64/386),
	// and fewer instructions (to calculate offset) on other architectures.
	done uint32
	m    Mutex
}
```

这是什么意思呢？

-   当需要访问struct的第一个字段时，我们可以直接对指针解引用来访问第一个字段。
-   要访问其他字段时，除了结构指针之外， 还需要提供与第一个字段的偏移量

在机器码中，这个偏移量是传递指令的附加值，这会使指令变得更长。对性能的影响是，CPU必须对结构指针添加偏移量以获取想要访问的字段的地址。

**因此访问struct的第一个字段的机器码更快，更加紧凑。**

这里假设字段在内存中的布局与结构定义中的布局相同，因为编译器可以决定改变内存中结构的字段顺序来优化存储空间，目前go编译器未做这样的优化。

这是一个小优化，在一些对性能优化有极致的要求的人是值得得关注的点。



# 1.13 引用类型与指针，有什么不同？

切片是一个引用类型，将它作为参数传入函数后，你在函数里对数据作变更是会实时反映到实参切片的。

```go
func foo(s []int)  {
	s[0] = 666
}

func main() {
	slice := []int{1,2}
	fmt.Println(slice) // [1 2]
	foo(slice)
	fmt.Println(slice) // [666 2]
}
```

此时切片这一引用类型，是不是有点像指针的效果？是的。

但它又和指针不一样，这一点主要体现在：在形参中所作的操作并不一定都会反映在实参上。

还是以切片为例，我在形参上对切片进行扩容，发现形参扩容后，实参并没有发生改变。

```go
func foo(s []int)  {
	s = append(s, 666)
}

func main() {
	slice := []int{1,2}
	fmt.Println(slice) // [1 2]
	foo(slice)
	fmt.Println(slice) // [1 2]
}
```

这是为什么呢？

这是因为当你对一个切片 append 的时候，它会做这些事情：

1.   新建一个新的切片 slice2，其实长度与 slice1 一样，但容量是 slice1 的两倍，此时 slice2 底层指向的匿名数组和 slice1 不是同一个。
2.   将 slice1 底层的数组的元素，一个一个的拷贝给 slice2 底层的数组。
3.   并把扩容的元素也拷贝到 slice2中
4.   最后把新的 slice2 返回回来，这就是为什么指针不用返回，而 slice.append 也要返回的原因

从这个流程中，可以看到等号左边的 s （slice2）和 等号右边的 s （slice1）底层引用的数组已经不是同一个了

```go
s = append(s, 666)
```

因此切片的形参做扩容，并不会影响到实参。

# 1.14 Go 是值传递，还是引用传递、指针传递？

Golang中函数的参数为切片时是传引用还是传值？

对于这个问题，可能会有很多认为是传引用，就比如下面这段代码

```go
func foo(s []int)  {
	s[0] = 666
}

func main() {
	slice := []int{1,2}
	fmt.Println(slice) // [1 2]
	foo(slice)
	fmt.Println(slice) // [666 2]
}
```

如果你不了解 Go 中切片的底层结构，你很可能会误信上面的观点。

但其实不是，**Go语言中都是值传递，而不是引用传递，也不是指针传递**。

Go 中切片的底层结构是这样的

```go
type slice struct {
    array unsafe.Pointer
    len   int
    cap   int
}
```

而当你将切片作为实参传给函数时，函数是会拷贝一份实参的结构和数据，生成另一个切片，实参切片和形参切片，不仅是长度、容量相等，连指向底层数组的指针都是一样的。

通过分别打印实参切片和形参切片的指针地址，就能验证这一观点

```go
func foo(s []int)  {
	fmt.Printf("%p \n", &s) // 0xc00000c080 
	s = append(s, 666)
}

func main() {
	slice := []int{1,2}
	fmt.Printf("%p \n", &slice)  // 0xc00000c060 
	foo(slice)
	fmt.Printf("%p \n", &slice)  // 0xc00000c060 
}
```

# 1.15 Go中哪些是可寻址，哪些是不可寻址的？

## 什么叫可寻址？

可直接使用 `&` 操作符取地址的对象，就是可寻址的（Addressable）。比如下面这个例子

```go
func main() {
	name := "iswbm"
	fmt.Println(&name) 
	// output: 0xc000010200
}
```

程序运行不会报错，说明 name 这个变量是可寻址的。

但不能说 `"iswbm"` 这个字符串是可寻址的。

`"iswbm"`  是字符串，字符串都是不可变的，是不可寻址的，后面会介绍到。

在开始逐个介绍之前，先说一下结论

-   指针可以寻址：`&Profile{}`
-   变量可以寻址：`name := Profile{}`
-   字面量通通不能寻址：`Profile{}`

## 哪些是可以寻址的？

### 变量：&x

```go
func main() {
	name := "iswbm"
	fmt.Println(&name) 
	// output: 0xc000010200
}
```

### 指针：&*x

```go
type Profile struct {
	Name string
}

func main() {
	fmt.Println(unsafe.Pointer(&Profile{Name: "iswbm"}))
    // output: 0xc000108040
}
```

### 数组元素索引: &a[0]

```go
func main() {
	s := [...]int{1,2,3}
	fmt.Println(&s[0])
	// output: xc0000b4010
}
```

### 切片

```go
func main() {
	fmt.Println([]int{1, 2, 3}[1:])
}
```

### 切片元素索引：&s[1]

```go
func main() {
	s := make([]int , 2, 2)
	fmt.Println(&s[0]) 
    // output: xc0000b4010
}
```

### 组合字面量: &struct{X type}{value}

所有的组合字面量都是不可寻址的，就像下面这样子

```go
type Profile struct {
	Name string
}

func new() Profile {
	return Profile{Name: "iswbm"}
}

func main() {
	fmt.Println(&new())
    // cannot take the address of new()
}
```

注意上面写法与这个写法的区别，下面这个写法代表不同意思，其中的 `&` 并不是取地址的操作，而代表实例化一个结构体的指针。

```go
type Profile struct {
	Name string
}

func main() {
	fmt.Println(&Profile{Name: "iswbm"}) // ok
}
```

虽然组合字面量是不可寻址的，但却可以对组合字面量的字段属性进行寻址（直接访问）

```go
type Profile struct {
	Name string
}

func new() Profile {
	return Profile{Name: "iswbm"}
}

func main() {
	fmt.Println(new().Name)
}
```

## 哪些是不可以寻址的？

### 常量

```go
import "fmt"

const VERSION  = "1.0"

func main() {
	fmt.Println(&VERSION)
}
```

### 字符串

```go
func getStr() string {
	return "iswbm"
}
func main() {
	fmt.Println(&getStr())
	// cannot take the address of getStr()
}
```

### 函数或方法

```go
func getStr() string {
	return "iswbm"
}
func main() {
	fmt.Println(&getStr)
	// cannot take the address of getStr
}
```

### 基本类型字面量

字面量分：**基本类型字面量** 和 **复合型字面量**。

基本类型字面量，是一个值的文本表示，都是不应该也是不可以被寻址的。

```go
func getInt() int {
	return 1024
}

func main() {
	fmt.Println(&getInt())
	// cannot take the address of getInt()
}
```

### map 中的元素

字典比较特殊，可以从两个角度来反向推导，假设字典的元素是可寻址的，会出现 什么问题？

1.   如果字典的元素不存在，则返回零值，而零值是不可变对象，如果能寻址问题就大了。
2.   而如果字典的元素存在，考虑到 Go 中 map 实现中元素的地址是变化的，这意味着寻址的结果也是无意义的。

基于这两点，Map 中的元素不可寻址，符合常理。

```go
func main() {
	p := map[string]string {
		"name": "iswbm",
	}

	fmt.Println(&p["name"])
	// cannot take the address of p["name"]
}
```

搞懂了这点，你应该能够理解下面这段代码为什么会报错啦~

```go
package main
 
import "fmt"
 
type Person struct {
    Name  string
    Email string
}
 
func main() {
    m := map[int]Person{
        1:Person{"Andy", "1137291867@qq.com"},
        2:Person{"Tiny", "qishuai231@gmail.com"},
        3:Person{"Jack", "qs_edu2009@163.com"},
    }
    
    //编译错误：cannot assign to struct field m[1].Name in map
    m[1].Name = "Scrapup"
```

### 数组字面量

数组字面量是不可寻址的，当你对数组字面量进行切片操作，其实就是寻找内部元素的地址，下面这段代码是会报错的

```go
func main() {
	fmt.Println([3]int{1, 2, 3}[1:])
	// invalid operation [3]int literal[1:] (slice of unaddressable value)
}
```

