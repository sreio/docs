> sonic 一个速度奇快的 JSON 序列化/反序列化库，由 JIT （即时编译）和 SIMD （单指令流多数据流）加速。

- Readme: https://github.com/bytedance/sonic/blob/main/README_ZH_CN.md
- GitHub仓库地址： https://github.com/bytedance/sonic


## 序列化/反序列化

默认的行为基本上与 `encoding/json` 相一致，除了 HTML 转义形式（参见 Escape HTML) 和 SortKeys 功能（参见 Sort Keys）没有遵循 RFC8259 。

```go
import "github.com/bytedance/sonic"

var data YourSchema
// Marshal
output, err := sonic.Marshal(&data)
// Unmarshal
err := sonic.Unmarshal(output, &data)
```


