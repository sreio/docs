### worker pool（goroutine池）

- 本质上是生产者消费者模型
- 可以有效控制goroutine数量，防止暴涨
- 需求：
    - 计算一个数字的各个位数之和，例如数字123，结果为1+2+3=6
    - 随机生成数字进行计算

控制台输出结果如下：

```go
package main

import (
    "fmt"
    "math/rand"
)

type Job struct {
    // id
    Id int
    // 需要计算的随机数
    RandNum int
}

type Result struct {
    // 这里必须传对象实例
    job *Job
    // 求和
    sum int
}

func main() {
    // 需要2个管道
    // 1.job管道
    jobChan := make(chan *Job, 128)
    // 2.结果管道
    resultChan := make(chan *Result, 128)
    // 3.创建工作池
    createPool(64, jobChan, resultChan)
    // 4.开个打印的协程
    go func(resultChan chan *Result) {
        // 遍历结果管道打印
        for result := range resultChan {
            fmt.Printf("job id:%v randnum:%v result:%d\n", result.job.Id,
                result.job.RandNum, result.sum)
        }
    }(resultChan)
    var id int
    // 循环创建job，输入到管道
    for {
        id++
        // 生成随机数
        r_num := rand.Int()
        job := &Job{
            Id:      id,
            RandNum: r_num,
        }
        jobChan <- job
    }
}

// 创建工作池
// 参数1：开几个协程
func createPool(num int, jobChan chan *Job, resultChan chan *Result) {
    // 根据开协程个数，去跑运行
    for i := 0; i < num; i++ {
        go func(jobChan chan *Job, resultChan chan *Result) {
            // 执行运算
            // 遍历job管道所有数据，进行相加
            for job := range jobChan {
                // 随机数接过来
                r_num := job.RandNum
                // 随机数每一位相加
                // 定义返回值
                var sum int
                for r_num != 0 {
                    tmp := r_num % 10
                    sum += tmp
                    r_num /= 10
                }
                // 想要的结果是Result
                r := &Result{
                    job: job,
                    sum: sum,
                }
                //运算结果扔到管道
                resultChan <- r
            }
        }(jobChan, resultChan)
    }
}
```

```txt
job id:1 randnum:5577006791947779410 result:95
job id:2 randnum:8674665223082153551 result:79
job id:3 randnum:6129484611666145821 result:81
job id:4 randnum:4037200794235010051 result:53
job id:5 randnum:3916589616287113937 result:95
job id:6 randnum:6334824724549167320 result:80
job id:7 randnum:605394647632969758 result:99
job id:8 randnum:1443635317331776148 result:77
job id:9 randnum:894385949183117216 result:89
job id:10 randnum:2775422040480279449 result:80
job id:11 randnum:4751997750760398084 result:99
job id:12 randnum:7504504064263669287 result:84
job id:13 randnum:1976235410884491574 result:88
job id:14 randnum:3510942875414458836 result:87
job id:15 randnum:2933568871211445515 result:80
job id:16 randnum:4324745483838182873 result:92
job id:17 randnum:2610529275472644968 result:89
...
```