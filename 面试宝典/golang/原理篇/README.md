#

# 3.1 局部变量分配在栈上还是堆上？

## 什么是堆内存和栈内存？

根据内存管理（分配和回收）方式的不同，可以将内存分为 **堆内存** 和 **栈内存**。

那么他们有什么区别呢？

**堆内存**：由内存分配器和垃圾收集器负责回收

**栈内存**：由编译器自动进行分配和释放

一个程序运行过程中，也许会有多个栈内存，但肯定只会有一个堆内存。

每个栈内存都是由线程或者协程独立占有，因此从栈中分配内存不需要加锁，并且栈内存在函数结束后会自动回收，性能相对堆内存好要高。

而堆内存呢？由于多个线程或者协程都有可能同时从堆中申请内存，因此在堆中申请内存需要加锁，避免造成冲突，并且堆内存在函数结束后，需要 GC （垃圾回收）的介入参与，如果有大量的 GC 操作，将会吏程序性能下降得历害。

## 局部变量是从哪里分配的？

在函数里声明定义的变量，我们称之为局部变量。

一般来说，局部变量的作用域仅在该函数中，当函数返回后，所有局部变量所占用的内存空间都将被收回，对于这类变量，都是从栈上分配内存空间，这一点大家应该是没有争议的。

可有一种局部变量，比较特殊。

这种局部变量，虽然在函数里声明定义，但是在函数外还会持续的使用。

对于这类局部变量，显然我们是不希望函数退出后将其销毁的。

那怎么办呢？可以从堆区分配内存空间给这类局部变量。

不过这个事实其实不用程序员操心，Go 的编译器会自行判断做优化的。但我们仍然需要知道这个知识点（因为面试会问哈哈）

# 3.2 为什么常量、字符串和字典不可寻址？

## 常量

首先要明白一件事，什么叫不可寻址？它指的是，不能通过 `&` 来获得内存地址的行为。

以常量为例

```go
import "fmt"

const VERSION  = "1.0"

func main() {
	fmt.Println(&VERSION)
}
```

编译的时候会直接报错说：无法取得 VERSION 的地址。**因为它是常量**

```sh
$ go build main.go
# command-line-arguments
./main.go:8:14: cannot take the address of VERSION
```

这其实还挺容易理解的，如果常量可以寻址的话，我们就可以通过指针修改常数的值，这无疑破坏了常数的定义。

## 字典

那么字典里的元素呢？为什么它不可以寻址？

```go
func main() {
	p := map[string]string {
		"name": "iswbm",
	}

	fmt.Println(&p["name"])
	// cannot take the address of p["name"]
}
```

它比较特殊，可以从两个角度来反向推导，假设字典的元素是可寻址的，会出现什么问题呢？

字典的使用无非两种情况，我们分别来探讨一下

1.   如果字典的元素不存在，则返回零值，而零值是不可变对象，这种情况要可寻址就有问题了
2.   而如果字典的元素存在，考虑到 Go 中 map 实现中元素的地址是变化的，前一秒 key1 对应 value1 ，下一秒可能就对应 value2 了，你取其地址，不一定能对应更改到 map 中 key1 的值，这意味着寻址的结果也是无意义的。

基于这两点，Map 中的元素不可寻址，符合常理。

## 字符串中的字节或者字符

字符串是不可变的，你想要修改其中某个字符，是做不到的。

```go
func main() {
	name := "iswbm"
	name[0] = 'x'  //  can not assign to name[0]
}
```

因此字符串的字节或者字符不可寻址，没有问题。

注意区分如下这种写法

`name = "golang"` 只是将 name 指向了一个新的内存地址，这个新的内存地址存的是值是 `golang`，实际上你并没有改变原字符串 `iswbm` 的内存地址里存储的值。

```go
func main() {
	name := "iswbm"
	name = "golang"
	fmt.Println(name)
}
```



# 3.3 为什么 slice 元素是可寻址的？

因为 slice 底层结构其实是一个匿名数组，既然数组的元素是可寻址的，那切片的元素自然也可以寻址。

```go
type slice struct {
    array unsafe.Pointer
    len   int
    cap   int
}
```

# 3.4 Go 的默认栈大小是多少？最大值多少？

Go 语言使用用户态线程 Goroutine 作为执行上下文，它的额外开销和默认栈大小都比线程小很多，然而 Goroutine 的栈内存空间和栈结构也在早期几个版本中发生过一些变化：

-   v1.0 ~ v1.1 — 最小栈内存空间为 4KB；
-   v1.2 — 将最小栈内存提升到了 8KB；
-   v1.3 — 使用连续栈替换之前版本的分段栈；
-   v1.4 — 将最小栈内存降低到了 2KB；

Goroutine 的初始栈内存在最初的几个版本中多次修改，从 4KB 提升到 8KB 是临时的解决方案，其目的是为了减轻分段栈的栈热分裂问题对程序造成的性能影响；

在 v1.3 版本引入连续栈之后，Goroutine 的初始栈大小降低到了 2KB，进一步减少了 Goroutine 占用的内存空间。

这个栈比 x86_64 构架下线程的默认栈 2M 要小很多，真的是轻量级的用户态线程。

关于这个初始值和最大值嘛，在 Go 的源码 `runtime/stack.go` 里其实都可以找到

```go
// rumtime.stack.go
// The minimum size of stack used by Go code
_StackMin = 2048

var maxstacksize uintptr = 1 << 20 // enough until runtime.main sets it for real
```

那么这个 `1<<20` 代表多大呢？使用 Python 计算一下是 1G

```python
>>> 1<<20
1048576
>>> 1048576/1024/1024
1.0
```

# 3.5 Go 中的分段栈和连续栈的区别？

## 分段栈

在 Go 1.3 版本之前 ，使用的栈结构是分段栈，随着`goroutine` 调用的函数层级的深入或者局部变量需要的越来越多时，运行时会调用 `runtime.morestack` 和 `runtime.newstack`创建一个新的栈空间，这些栈空间是不连续的，但是当前 `goroutine` 的多个栈空间会以双向链表的形式串联起来，运行时会通过指针找到连续的栈片段。

分段栈虽然能够按需为当前 `goroutine` 分配内存并且及时减少内存的占用，但是它也存在一个比较大的问题：

如果当前 `goroutine` 的栈几乎充满，那么任意的函数调用都会触发栈的扩容，当函数返回后又会触发栈的收缩，如果在一个循环中调用函数，栈的分配和释放就会造成巨大的额外开销，这被称为热分裂问题（Hot split）。

为了解决这个问题，Go 在 1.2 版本的时候不得不将栈的初始化内存从 4KB 增大到了 8KB。后来把采用连续栈结构后，又把初始栈大小减小到了 2KB。

## 连续栈

连续栈可以解决分段栈中存在的两个问题，其核心原理就是每当程序的栈空间不足时，初始化一片比旧栈大两倍的新栈并将原栈中的所有值都迁移到新的栈中，新的局部变量或者函数调用就有了充足的内存空间。使用连续栈机制时，栈空间不足导致的扩容会经历以下几个步骤：

1.  调用用`runtime.newstack`在内存空间中分配更大的栈内存空间；
2.  使用`runtime.copystack`将旧栈中的所有内容复制到新的栈中；
3.  将指向旧栈对应变量的指针重新指向新栈；
4.  调用`runtime.stackfree`销毁并回收旧栈的内存空间；

`copystack`会把旧栈里的所有内容拷贝到新栈里然后调整所有指向旧栈的变量的指针指向到新栈， 我们可以用下面这个程序验证下，栈扩容后同一个变量的内存地址会发生变化。

```go
package main

func main() {
	var x [10]int
	println(&x)
	a(x)
	println(&x)
}

//go:noinline
func a(x [10]int) {
	println(`func a`)
	var y [100]int
	b(y)
}

//go:noinline
func b(x [100]int) {
	println(`func b`)
	var y [1000]int
	c(y)
}

//go:noinline
func c(x [1000]int) {
	println(`func c`)
}
```

程序的输出可以看到在栈扩容前后，变量`x`的内存地址的变化：

```go
0xc000030738
...
...
0xc000081f38
```

# 3.6 内存对齐、内存布局是怎么回事？

## 字长（word size）

字长（word size），指的是 CPU 一次可以访问数据的最大长度：

-   对于 32 位的 cpu 来说：word size 为 `2^32`，即 4 byte
-   对于 64 位的 cpu 来说：word size 为 `2^64`，即 8 byte

## 两种内存布局

下边是一个结构体的两种内存布局方式

### 第一种：按顺序

代码

```go
type Foo struct {
	A int8 // 1
	B int8 // 1
	C int8 // 1
}

type Bar struct {
	x int32 // 4
	y *Foo  // 8
	z bool  // 1
}
```

你觉得 Bar 对象会占用多少的内存？ 可能很多人会下意识地回答 **13**

会回答 13 是因为你觉得该结构体的内存分配是下面这样按顺利分配的。

按照前面所介绍的 word size 为 8 来计算，使用这种分配方式，当你访问 bar.y 的时候，CPU 需要访问内存两次。

![memory layout of Bar1](http://image.iswbm.com/20210925153036.png)

### 第二种：按字长

而如果使用下面这种方式，当你再次访问 bar.y 的时候，CPU 需要访问内存一次。

![memory layout of Bar1](http://image.iswbm.com/20210925153041.png)

因此真正的答案是 **24**，这是一种典型的用空间换时间的方法 -- **内存对齐**

```go
func main() {
	var bar Bar
	fmt.Println(unsafe.Sizeof(bar))  // 24
}
```

## 合理定义结构体

从以上可以发现，我们定义的结构体虽然不大，只占用 24个byte，但实际有用的只有 13 的byte，内存使用率只有 50% 左右，很有优化的必要性。

如果将第三个属性挪 `y` 的前面

![memory layout of Bar2](http://image.iswbm.com/20210925154455.png)

那就可以省下来 1 个 byte 了

```go
func main() {
	var bar Bar
	fmt.Println(unsafe.Sizeof(bar))  // 16
}
```

## y 为什么占用 8 字节？

看完了上面的介绍，想必你一定有一个疑问： Foo 结构体实际占用 3个byte，为什么 Bar.y 却要占用 8个 byte 呢？

```go
type Foo struct {
	A int8 // 1
	B int8 // 1
	C int8 // 1
}

type Bar struct {
	x int32 // 4
	y *Foo  // 8
	z bool  // 1
}
```

因为 `Bar.y` 表示的是一个指针，而指针的对齐系数是 8

```go
func main() {
	var bar Bar
	fmt.Println(unsafe.Alignof(bar.y))  // 8
}
```

你大可将 y 改成普通对象

```go
type Foo struct {
	A int8 // 1
	B int8 // 1
	C int8 // 1
}

type Bar struct {
	x int32 // 4
	y Foo  // 3
	z bool  // 1
}
```

这样一来，bar 对象就只占用一个字长 

```go
func main() {
	var bar Bar
	fmt.Println(unsafe.Sizeof(bar)) // 8
}
```
# 3.7 Go 里是怎么比较相等与否？

## 1. 两个 interface 比较

interface 的内部实现包含了两个字段，一个是 type，一个是 data

![](http://image.iswbm.com/20200610235106.png)

因此两个 interface 比较，势必与这两个字段有所关系。

经过验证，只有下面两种情况，两个 interface 才会相等。

### 第一种情况

**type 和 data 都相等**

在下面的代码中，p1 和 p2 的 type 都是 Profile，data 都是 `{"iswbm"}`，因此 p1 与 p2 相等

而 p3 和 p3 虽然类型都是 `*Profile`，但由于 data 存储的是结构体的地址，而两个地址和不相同，因此 p3 与 p4 不相等

```go
package main

import "fmt"

type Profile struct {
	Name string
}

type ProfileInt interface {}

func main()  {
	var p1, p2 ProfileInt = Profile{"iswbm"}, Profile{"iswbm"}
	var p3, p4 ProfileInt = &Profile{"iswbm"}, &Profile{"iswbm"}

	fmt.Printf("p1 --> type: %T, data: %v \n", p1, p1)
	fmt.Printf("p2 --> type: %T, data: %v \n", p2, p2)
	fmt.Println(p1 == p2)  // true

	fmt.Printf("p3 --> type: %T, data: %p \n", p3, p3)
	fmt.Printf("p4 --> type: %T, data: %p \n", p4, p4)
	fmt.Println(p3 == p4)  // false
}
```

运行后，输出如下

```
p1 --> type: main.Profile, data: {iswbm} 
p2 --> type: main.Profile, data: {iswbm} 
true
p3 --> type: *main.Profile, data: 0xc00008e200 
p4 --> type: *main.Profile, data: 0xc00008e210 
false
```

### 第二种情况

**特殊情况：两个 interface 都是 nil **

当一个 interface 的 type 和 data 都处于 unset 状态的时候，那么该 interface 的值就为 nil

```go
package main

import "fmt"

type ProfileInt interface {}

func main()  {
	var p1, p2 ProfileInt
	fmt.Println(p1==p2) // true
}
```



## 2. interface 与 非 interface 比较

当 interface 与非 interface 比较时，会将 非interface 转换成 interface ，然后再按照 **两个 interface 比较** 的规则进行比较。

示例如下

```go
package main

import (
	"fmt"
	"reflect"
)

func main()  {
	var a string = "iswbm"
	var b interface{} = "iswbm"
	fmt.Println(a==b) // true
}
```

上面这种例子可能还好理解，那么请你看下面这个例子，为什么经过反射看到的他们不相等？

```go
package main

import (
	"fmt"
	"reflect"
)

func main()  {
	var a *string = nil
	var b interface{} = a

	fmt.Println(b==nil) // false
}
```

因此当 nil 转换为interface 后是 `(type=nil, data=nil)` ，这与 b `(type=*string, data=nil)`  虽然 data 是一样的，但 type 不相等，因此他们并不相等。



# 3.8 所有的 T 类型都有 *T 类型吗？

`*T` 类型的对象指的是类型是 T 的对象的指针，很明显，只有当 T 类型的对象，是可以寻址的情况，才可以取到其指针。

诸如字符串、map 的元素、常量、包级别的函数，都是不可寻址的，它们都没有对应的 `*T` 类型

随便举个常量的例子

```go
package main

import "fmt"

type T string

func (T *T) say() {
	fmt.Println("hello")
}

func main() {
	const NAME T = "iswbm"
	NAME.say()
}
```

报错如下

```go
./demo.go:13:6: cannot call pointer method on NAME
./demo.go:13:6: cannot take the address of NAME
```

# 3.9 数组对比切片有哪些优势？

## 1. 编译检查越界

由于数组在声明后，长度就是固定的，因此在编译的时候编译器可以检查在索引取值的时候，是否有越界

```go
func main() {
	array := [2]int{}
	array[2] = 2  //invalid array index 2 (out of bounds for 2-element array)
}
```

而切片的长度只有运行时才能知晓，编译器无法检查。

## 2. 长度是类型的一部分

在声明一个数组的类型时，需要指明两点：元素的类型和元素的个数。

```go
var array [2]int
```

因此长度是数组类型的一部分，两个元素类型相同，但可包含的元素个数不同的数组，属于两个类型。

```go
func main() {
	var array1 [2]int
	var array2 [2]int
	var array3 [3]int
	fmt.Println(reflect.TypeOf(array1) == reflect.TypeOf(array2)) // true
	fmt.Println(reflect.TypeOf(array1) == reflect.TypeOf(array3)) // false
}
```

基于这个特点，可以用它来达到一些合法性校验的目的，例如 IPv4 的地址可以声明为 [4]byte，符合该类型的数组就是合法的 ip，反之则不合法。

## 3. 数组可以比较

类型相同的两个数组可以进行比较

```go
func main() {
	array1 := [2]int{1,2}
	array2 := [2]int{1,2}
	array3 := [2]int{2,1}
	fmt.Println(array1 == array2) // true
	fmt.Println(array1 == array3) // false
}
```

类型不同（长度不同）的数组 和 切片均不行。

可比较这一特性，决定了数组也可以用来当 map 的 key 使用。

```go
func main() {
	array1 := [2]int{1,2}
	dict := make(map[[2]int]string)
	dict[array1] = "hello"
	fmt.Println(dict) // map[[1 2]:hello]
}
```
# 3.10 GMP 偷取 G 为什么不需要加锁？

在之前的文章中，想必你已经知道了 GMP 模型的工作原理，其中有一个非常重要的问题，这个问题非常的细节，但非常值得拿出来讲一下。

P 从全局队列里取 G 的时候，由于可能有多个 P 同时取G 的情况，因此需要加锁，这很容易理解。然后 P 从本地队列里取 G 的时候，正常情况下，只有自己取 G，不用加锁也没关系。但问题就在于当有其他的 P 处于自旋状态的时候，就有可能来自己这边偷。

如此看来，P 的本地队列也有并发竞争的问题，可为什么网上的文章都说从 P 本地队列里的时候，也不用加锁呢？

难道是这些人，瞎说的？

其实啊，这些人说的并没有错，只是他们没把事情说清楚。

原来 P 从本地队列取 G 的这个操作，是一个 CAS 操作，它具有原子性，是由硬件直接支持的，不需要并发的竞争关系。

而我们常见的加锁操作来避免并发的竞争问题，是从操作系统层面来实现的。

因此 GMP 中偷取 G 的过程也是不需要加锁的噢。

CAS 的原子操作虽然可以让程序员变得简单，有时也要付出一定的代价，使用 CAS 有两个小问题：

1.   使用 CAS 为保证执行成功，程序需要用 for 循环不断尝试，直到成功才返回，因此如果 CAS 长时间不成功，就会阻塞其他硬件对CPU的访问，开销比较大。
2.   每次使用 CAS 会原子操作时，一般只能对一个共享变量做操作，若要对多个共享变量操作，循环 CAS 可能就不太做，不过应该可以通过把多个变量放在一个对象里来进行 CAS 操作。

为了让你对 CAS 有一个直观的理解，这里直接放上网上找的一段使用 atomic 包实现的 CAS 操作代码

```go
package main

import (
"fmt"
"sync"
"sync/atomic"
)

var (
	counter int32//计数器
	wg sync.WaitGroup //信号量
)

func main() {
	threadNum := 5 //1. 五个信号量
	wg.Add(threadNum) //2.开启5个线程
	for i := 0; i < threadNum; i++ {
		go incCounter(i)
	}
	//3.等待子线程结束
	wg.Wait()
	fmt.Println(counter)
}

func incCounter(index int) {
	defer wg.Done()
	spinNum := 0
	for {
		//2.1原子操作
		old := counter
		ok := atomic.CompareAndSwapInt32(&counter, old, old+1)
		if ok {
			break
		} else {
			spinNum++
		}
	}
	fmt.Printf("thread,%d,spinnum,%d\n",index,spinNum)
}
```





# 3.11 堆引用栈内存是怎么回收的？

## 3.11.1. 内存管理概述

Go 语言通过`垃圾回收`（GC）来管理内存，主要用于回收`堆内存`。

## 3.11.2. 栈内存和堆内存的区别
- `栈内存`：用于函数调用和局部变量，自动分配和释放。
- `堆内存`：用于动态分配，需要垃圾回收器管理。

## 3.11.3. 垃圾回收机制

使用三色标记-清除算法：

- `标记阶段`：从根对象开始，递归标记所有可达对象。
- `清除阶段`：回收未被标记的对象。

## 3.11.4. 栈内存引用的回收

当函数返回时，函数栈帧中的局部变量被销毁。

垃圾回收器在标记阶段扫描栈帧，标记引用的堆对象。

## 3.11.5. 优化和注意事项

- `逃逸分析`：决定变量分配在栈上还是堆上。
- `减少不必要的分配`：优化性能，减少 GC 频率。
- `调优 GC 参数`：根据需求调整（如 GOGC）优化性能。

## 3.11.6 面试回答示例

> Go 语言通过`垃圾回收器`管理内存，主要用于堆内存回收。
> 
> `栈内存`用于`函数调用`和`局部变量`的分配，自动管理。
> 
> `堆内存`则依赖于`垃圾回收器`，采用三色标记-清除算法进行管理。
> 
> 在函数返回时，栈帧中的局部变量销毁，垃圾回收器在标记阶段扫描栈帧中的引用，从而决定是否回收堆内存。
> 
> 通过逃逸分析，编译器决定变量的分配位置，并通过优化分配和调整 GC 参数来提升性能。
