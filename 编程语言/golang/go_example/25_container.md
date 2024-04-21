## heap 堆操作

```go
package main

import "container/heap"

// heap包实现了对任意类型（实现了heap.Interface接口）的堆操作
func main() {

	// 初始化结构体
	h := &IntHeap{1, 4, 2, 7, 8, 9, 3, 6}
	// 初始化堆
	heap.Init(h)
	// 添加元素并重新排序
	heap.Push(h, 5)
	// 移除最小元素返回一个新的interface,根据sort排序规则
	heap.Pop(h)
	// 移除指定位置的interface，返回一个新的interface
	heap.Remove(h, 5)
	// 移除指定位置的interface，并修复索引
	heap.Fix(h, 5)
}

// 声明结构体
type IntHeap []int

// 创建sort.Interface接口的Len方法
func (h IntHeap) Len() int { return len(h) }

// 创建sort.Interface接口的Less方法
func (h IntHeap) Less(i, j int) bool { return h[i] < h[j] }

// 创建sort.Interface接口的Swap方法
func (h IntHeap) Swap(i, j int) { h[i], h[j] = h[j], h[i] }

// 创建heap.Interface接口的添加方法
func (h *IntHeap) Push(x interface{}) { *h = append(*h, x.(int)) }

// 创建heap.Interface接口的移除方法
func (h *IntHeap) Pop() interface{} {
	old := *h
	n := len(old)
	x := old[n-1]
	*h = old[0 : n-1]
	return x
}
```


## list 双向链表

```go
package main

import (
	"container/list"
	"fmt"
)

// list包实现双向链表
func main() {

	// 初始化链表
	l := list.New()
	l2 := list.New()

	// 向链表后面插入值为v的元素并返回一个新的元素
	e4 := l.PushBack(4)
	// 向链表后面插入另一个链表,l和l2可以相同，但是都不能为nil
	l.PushBackList(l2)
	// 向链表前面面插入值为v的元素并返回一个新的元素
	e1 := l.PushFront(1)
	// 向链表前面插入另一个链表,两个链表可以相同，但是都不能为nil
	l.PushFrontList(l2)
	// 向元素mark前插入值为v的元素并返回一个新的元素，如果mark不是链表的元素，则不改变链表，mark不能为nil
	l.InsertBefore(3, e4)
	// 向元素mark后插入值为v的元素并返回一个新的元素，如果mark不是链表的元素，则不改变链表，mark不能为nil
	l.InsertAfter(2, e1)

	// 如果元素存在链表中，从链表中移除元素，并返回元素值，元素值不能为nil
	l.Remove(e1)
	// 如果元素存在链表中，将元素移动到链表最前面，元素不能为nil
	l.MoveToFront(e4)
	// 如果元素存在链表中，将元素移动到链表最后面，元素不能为nil
	l.MoveToBack(e1)
	// 如果元素e和mark都存在链表中，将e移动到mark前面，两个元素都不能为nil
	l.MoveBefore(e1, e4)
	// 如果元素e和mark都存在链表中，将e移动到mark后面，两个元素都不能为nil
	l.MoveAfter(e1, e4)

	// 返回链表长度
	l.Len()

	// 遍历链表从前面开始打印内容
	fmt.Println("front: ")
	for e := l.Front(); e != nil; e = e.Next() {
		fmt.Println(e.Value)
	}
	// 遍历链表从后面开始打印内容
	fmt.Println("back: ")
	for e := l.Back(); e != nil; e = e.Prev() {
		fmt.Println(e.Value)
	}
}
```

## ring 链表

```go
package main

import (
	"container/ring"
	"fmt"
)

// ring包实现环形链表
func main() {

	// 初始化n个元素的环形链表
	r := ring.New(5)
	s := ring.New(5)
	// 返回链表长度
	n := r.Len()

	for i := 0; i < n; i++ {
		r.Value = i  // 给元素赋值
		r = r.Next() // 获取下一个元素

		s.Value = i
		s = s.Next()
	}

	for j := 0; j < n; j++ {
		r = r.Prev()         // 获取上一个元素
		fmt.Println(r.Value) //
	}

	// 循环访问环形链表所有元素
	r.Do(func(p interface{}) {
		fmt.Println(p.(int))
	})

	// 将前面n个元素移到后面，例：0,1,2,3,4,5 => 3,4,5,0,1,2
	r.Move(3)

	// 链表r与链表s是不同链表，则在r链表的后面链接s链表，否则删除相同部分
	r.Link(s)
	// 从下一个元素开始，移除链表连续n个元素
	r.Unlink(3)
}
```