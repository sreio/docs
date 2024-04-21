## runtime 

```go
package main

import (
	"fmt"
	"runtime"
	"strings"
)

// runtime包提供和go运行时环境的互操作，如控制go程的函数
// 它也包括用于reflect包的低层次类型信息
func main() {

	// 基础
	example()

	// 显示调用过程
	exampleFrames()
}

func example() {

	// 返回Go的根目录。如果存在GOROOT环境变量，返回该变量的值；否则，返回创建Go时的根目录
	fmt.Println(runtime.GOROOT())

	// 返回Go的版本字符串。它要么是递交的hash和创建时的日期；要么是发行标签如"go1.3"
	fmt.Println(runtime.Version())

	// 返回本地机器的逻辑CPU个数
	fmt.Println(runtime.NumCPU())

	// 返回当前进程执行的cgo调用次数
	fmt.Println(runtime.NumCgoCall())

	// 返回当前存在的Go程数
	fmt.Println(runtime.NumGoroutine())

	// 执行一次垃圾回收
	runtime.GC()

	// 设置可同时执行的最大CPU数，并返回先前的设置
	// 若 n < 1，它就不会更改当前设置
	// 本地机器的逻辑CPU数可通过 NumCPU 查询。本函数在调度程序优化后会去掉
	runtime.GOMAXPROCS(2)

	go func() {

		fmt.Println("Start Goroutine1")

		// 终止调用它的go程。其它go程不会受影响。Goexit会在终止该go程前执行所有defer的函数。
		// 在程序的main go程调用本函数，会终结该go程，而不会让main返回
		// 因为main函数没有返回，程序会继续执行其它的go程
		// 如果所有其它go程都退出了，程序会panic
		runtime.Goexit()

		fmt.Println("End Goroutine1")
	}()

	go func() {

		// 使当前go程放弃处理器，以让其它go程运行。它不会挂起当前go程，因此当前go程未来会恢复执行
		runtime.Gosched()
		fmt.Println("Start Goroutine2")
		fmt.Println("End Goroutine2")
	}()

	go func() {

		// 将调用的go程绑定到它当前所在的操作系统线程
		// 除非调用的go程退出或调用UnlockOSThread，否则它将总是在该线程中执行，而其它go程则不能进入该线程
		runtime.LockOSThread()
		fmt.Println("Start Goroutine3")
		fmt.Println("End Goroutine3")

		// 将调用的go程解除和它绑定的操作系统线程
		// 若调用的go程未调用LockOSThread，UnlockOSThread不做操作
		runtime.UnlockOSThread()
	}()
}

func exampleFrames() {

	c := func() {

		// 初始化一个长度为10的整数切片
		pc := make([]uintptr, 10)

		// 把当前go程调用栈上的调用栈标识符填入切片pc中，返回写入到pc中的项数
		// 实参skip为开始在pc中记录之前所要跳过的栈帧数，0表示Callers自身的调用栈，1表示Callers所在的调用栈
		n := runtime.Callers(0, pc)
		if n == 0 {
			return
		}

		// 截取有效部分
		pc = pc[:n]

		// 获取被调用方返回的PC值切片，并准备返回 函数/文件/行 信息
		// 在完成帧处理之前，不要更改切片
		frames := runtime.CallersFrames(pc)

		for {
			// 返回下一个调用者的帧信息
			// 如果 more为false，则无下一个（Frame 值有效）
			frame, more := frames.Next()

			// 判断文件名是否包含 "runtime/" 字符串
			if !strings.Contains(frame.File, "runtime/") {
				break
			}
			fmt.Printf("- more:%v | %s\n", more, frame.Function)
			if !more {
				break
			}
		}
	}
	b := func() { c() }
	a := func() { b() }

	a()
}
```


## runtime/debug

```go
package main

import (
	"fmt"
	"log"
	"os"
	"runtime"
	"runtime/debug"
	"time"
)

// debug包提供了程序在运行时进行调试的功能
func main() {

	// 强制进行一次垃圾收集，以释放尽量多的内存回操作系统。（即使没有调用，运行时环境也会在后台任务里逐渐将内存释放给系统）
	debug.FreeOSMemory()

	// 返回嵌入在运行二进制文件中的构建信息
	// 该信息仅在使用模块支持构建的二进制文件中可用
	if info, ok := debug.ReadBuildInfo(); ok {

		// main包路径
		fmt.Println(info.Path)
		// module信息
		fmt.Println(info.Main)
		// module依赖
		fmt.Println(info.Deps)
	}

	// 设置垃圾回收百分比
	exampleGCPercent()

	// 设置被单个go协程调用栈可使用内存值
	exampleSetMaxStack()

	// 设置go程序可以使用的最大操作系统线程数
	exampleSetMaxThreads()

	// 设置程序请求运行是只触发panic,而不崩溃
	exampleSetPanic()

	// 垃圾收集信息
	exampleStats()

	// 将内存分配堆和其中对象的描述写入文件中
	exampleHeapDump()

	// 获取go协程调用栈踪迹
	exampleStack()
}

func exampleGCPercent() {

	// 设定垃圾收集的目标百分比
	// 当新申请的内存大小占前次垃圾收集剩余可用内存大小的比率达到设定值时，就会触发垃圾收集
	// SetGCPercent返回之前的设定。初始值为环境变量GOGC的值；如果没有设置该环境变量，初始值为100
	// percent参数如果是负数值，会关闭垃圾收集
	fmt.Println(debug.SetGCPercent(1))

	// 初始化切片
	var dic = make([]byte, 100, 100)

	// 将x的终止器设置为f
	// 当垃圾收集器发现一个不能接触的（即引用计数为零，程序中不能再直接或间接访问该对象）具有终止器的块时，它会清理该关联（对象到终止器）并在独立go程调用f(x)
	// 这使x再次可以接触，但没有了绑定的终止器。如果SetFinalizer没有被再次调用，下一次垃圾收集器将视x为不可接触的，并释放x
	// SetFinalizer(x, nil)会清理任何绑定到x的终止器
	runtime.SetFinalizer(&dic, func(dic *[]byte) {
		fmt.Println("内存回收1")
	})

	// 立即回收
	runtime.GC()

	var s = make([]byte, 100, 100)
	runtime.SetFinalizer(&s, func(dic *[]byte) {
		fmt.Println("内存回收2")
	})

	d := make([]byte, 300, 300)

	for index, _ := range d {
		d[index] = 'a'
	}
	fmt.Println(d)

	time.Sleep(time.Second)
}

func exampleSetMaxStack() {

	// 设置被单个go协程调用栈可使用的内存最大值
	// 修改该函数设置值为1，再次运行 exampleSetMaxStack()即可看到效果
	debug.SetMaxStack(102400)

	for i := 0; i < 10; i++ {
		go func() {
			fmt.Println(1)
		}()
	}
	time.Sleep(time.Second)
}

func exampleSetMaxThreads() {

	// 置go程序可以使用的最大操作系统线程数
	// 如果程序试图使用超过该限制的线程数，就会导致程序崩溃
	// SetMaxThreads返回之前的设置，初始设置为10000个线程
	// 主要用于限制程序无限制的创造线程导致的灾难。目的是让程序在干掉操作系统之前，先干掉它自己
	debug.SetMaxThreads(10)

	go func() {
		fmt.Println("Hello World!")
	}()
	time.Sleep(time.Second)
}

func exampleSetPanic() {

	go func() {
		defer func() { recover() }()

		// 控制程序在不期望（非nil）的地址出错时的运行时行为
		// 这些错误一般是因为运行时内存破坏的bug引起的，因此默认反应是使程序崩溃
		// 使用内存映射的文件或进行内存的不安全操作的程序可能会在非nil的地址出现错误；SetPanicOnFault允许这些程序请求运行时只触发一个panic，而不是崩溃
		// SetPanicOnFault只用于当前的go程。它返回之前的设置
		fmt.Println(debug.SetPanicOnFault(true))
		var s *int = nil
		*s = 34
	}()

	time.Sleep(time.Second)

	fmt.Println("ddd")
}

func exampleStats() {

	// 初始化切片
	data := make([]byte, 1000, 1000)
	println(data)

	// 回收
	runtime.GC()

	// 声明一个GCStats，用于收集了近期垃圾收集的信息
	var stats debug.GCStats

	// 将垃圾收集信息填入stats里
	debug.ReadGCStats(&stats)

	// 垃圾收集的次数
	fmt.Println(stats.NumGC)

	// 最近一次垃圾收集的时间
	fmt.Println(stats.LastGC)

	// 每次暂停收集垃圾的消耗的时间
	fmt.Println(stats.Pause)

	// 所有暂停收集垃圾消耗的总时间
	fmt.Println(stats.PauseTotal)

	// 暂停结束时间历史记录，最近的第一个
	fmt.Println(stats.PauseEnd)
}

func exampleHeapDump() {

	// 打开文件
	f, _ := os.OpenFile("testdata/debug_heapDump.txt", os.O_RDWR|os.O_CREATE, 0666)

	// 返回与文件f对应的整数类型的Unix文件描述符
	fd := f.Fd()

	// 将内存分配堆和其中对象的描述写入给定文件描述符fd指定的文件
	debug.WriteHeapDump(fd)

	data := make([]byte, 10, 10)
	println(data)
	runtime.GC()

	if err := f.Close(); err != nil {
		log.Fatal(err)
	}
}

func exampleStack() {

	go func() {
		// 返回格式化的go程的调用栈踪迹
		// 对于每一个调用栈，它包括原文件的行信息和PC值；对go函数还会尝试获取调用该函数的函数或方法，及调用所在行的文本
		fmt.Println(string(debug.Stack()))

		// 将Stack返回信息打印到标准错误输出
		// debug.PrintStack()
	}()
	time.Sleep(time.Second)
}
```


## runtime/pprof

```go
package main

import (
	"fmt"
	"log"
	"os"
	"runtime"
	"runtime/pprof"
)

// pprof包以pprof可视化工具期望的格式书写运行时剖面数据
// 采集程序的运行数据进行分析，使用 'go tool pprof' 命令进行分析
func main() {

	// 创建pprof文件
	// 使用 "go tool pprof testdata/test.prof" 进入，如 使用top查看数据，web生成svg文件
	f, err := os.Create("testdata/test.prof")
	if err != nil {
		log.Fatal(err)
	}

	// 设置CPU profile记录的速率为平均每秒hz次
	// 如果hz<=0，SetCPUProfileRate会关闭profile的记录。如果记录器在执行，该速率必须在关闭之后才能修改
	runtime.SetCPUProfileRate(10000)

	// 为当前进程开启CPU profile
	// 在分析时，分析报告会缓存并写入到w中。若分析已经开启，StartCPUProfile就会返回错误
	if err := pprof.StartCPUProfile(f); err != nil {
		log.Fatal(err)
	}
	// 停止当前的CPU profile（如果有）
	// 只会在所有的分析报告写入完毕后才会返回
	defer pprof.StopCPUProfile()

	// 下面写自己的程序

	for i := 0; i < 30; i++ {
		// 递归实现的斐波纳契数列
		num := fibonacci(i)
		fmt.Println(num)
	}
}

func fibonacci(num int) int {

	if num < 2 {
		return 1
	}
	return fibonacci(num-1) + fibonacci(num-2)
}
```

## runtime/trace

```go
package main

import (
	"fmt"
	"log"
	"os"
	"runtime/trace"
)

// 执行追踪器
// 跟踪器捕获各种各样的执行事件，如 goroutine 创建/阻塞/解锁，系统调用进入/退出/块，GC 相关事件，堆大小变化，处理器启动/停止等，并将它们以紧凑的形式写入 io.Writer 中
// 大多数事件都会捕获精确的纳秒精度时间戳和堆栈跟踪。跟踪可以稍后使用 'go tool trace' 命令进行分析
func main() {

	// 创建trace.out文件
	// 跟踪完毕后可以使用 "go tool trace testdata/trace.out" 命令分析
	f, err := os.Create("testdata/trace.out")
	if err != nil {
		log.Fatalf("failed to create trace output file: %v", err)
	}

	defer func() {
		if err := f.Close(); err != nil {
			log.Fatalf("failed to close trace file: %v", err)
		}
	}()

	// 启用当前程序的跟踪
	// 跟踪时，跟踪将被缓冲并写入 w
	// 如果跟踪已启用，则启动将返回错误
	if err := trace.Start(f); err != nil {
		log.Fatalf("failed to start trace: %v", err)
	}
	// 停止当前跟踪，如果有的话。在完成跟踪的所有写入后，仅停止返回
	defer trace.Stop()

	// 下面写自己的程序

	traceTest()
}

func traceTest() {
	fmt.Printf("this function will be traced\n")
}
```