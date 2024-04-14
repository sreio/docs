> pflag 可以直接替换 Go 的 flag 包，实现 POSIX/GNU 风格的 --flags 功能。



- GitHub仓库地址: https://github.com/spf13/pflag


## 使用方法

pflag 是 Go 原生 flag 包的直接替代品。如果以 "flag "为名导入 pflag，那么所有代码都将继续运行，不会有任何变化。

```go
import flag "github.com/spf13/pflag"
```