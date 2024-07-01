### 简介

> 记录学习中遇到的知识点和各种有趣的小技巧。

### 在线地址

- [srrio](https://docs.srrio.co)
- [github](https://sreio.github.io/docs/)
- [sreio](https://www.sreio.com/)

### 本地预览
```terminal
// 全局安装 docsify-cli 工具
npm i docsify-cli -g
// docsify serve 启动一个本地服务器
docsify serve .
```

### 相关链接 

- [nodejs Download](https://nodejs.org/en/download/current)
- [docsify](https://docsify.js.org/#/zh-cn/)
- [markdown](https://markdown.com.cn/)


```go
package main

import (
    "fmt"
    "time"
)

func main() {
    fmt.Printf("Hello, it's %s time.", time.Now().In(time.FixedZone("CST", 8*3600)).Format(time.DateTime))
}

```

<codapi-snippet sandbox="go" editor="basic">
</codapi-snippet>