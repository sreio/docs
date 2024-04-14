## 什么是 RPC ？

RPC 代指`远程过程调用`（Remote Procedure Call），它的调用包含了`传输协议`和`编码（对象序列号）协议`等等。允许运行于一台计算机的程序调用另一台计算机的子程序，而开发人员不需要了解底层通信细节的计算机软件通信协议。


## RPC 框架

|                    框架                    | 跨语言 | 多 IDL | 服务治理 | 注册中心 | 服务管理 |
| :----------------------------------------: | :----: | :----: | :------: | :------: | :------: |
|          [grpc](https://grpc.io/)          |   √    |   ×    |    ×     |    ×     |    ×     |
| [thrift](https://github.com/apache/thrift) |   √    |   ×    |    ×     |    ×     |    ×     |
| [rpcx](https://github.com/smallnest/rpcx)  |   ×    |   √    |    √     |    √     |    √     |
|  [dubbo](https://github.com/apache/dubbo)  |   ×    |   √    |    √     |    √     |    √     |

### gRPC

gRPC 是 Google 开源的一个高性能的 RPC(Remote Procedure Call) 框架，它具有如下的优点：

- 提供高效的进程间通信。`gRPC` 没有使用 XML 或者 JSON 这种文本格式，而是采用了基于 `protocol buffers` 的二进制协议；同时，`gRPC` 采用了 `HTTP/2` 做为通信协议，从而能够快速的处理进程间通信。
- 简单且良好的服务接口和模式。`gRPC` 为程序开发提供了一种契约优先的方式，必须首先定义服务接口，才能处理实现细节。
- 支持多语言。`gRPC` 是语言中立的，我们可以选择任意一种编程语言，都能够与 `gRPC` 客户端或者服务端进行交互。
- 成熟并且已被广泛使用。通过在 Google 的大量实战测试，`gRPC` 已经发展成熟。


![grpc](./img/grpc.svg)

### 讲解

1. 客户端（gRPC Sub）调用 A 方法，发起 RPC 调用
2. 对请求信息使用 Protobuf 进行对象序列化压缩（IDL）
3. 服务端（gRPC Server）接收到请求后，解码请求体，进行业务逻辑处理并返回
4. 对响应结果使用 Protobuf 进行对象序列化压缩（IDL）
5. 客户端接受到服务端响应，解码请求体。回调被调用的 A 方法，唤醒正在等待响应（阻塞）的客户端调用并返回响应结果


> IDL（Interface description language）是指接口描述语言，是用来描述软件组件接口的一种计算机语言，是跨平台开发的基础。IDL通过一种中立的方式来描述接口，使得在不同平台上运行的对象和用不同语言编写的程序可以相互通信交流；比如，一个组件用C++写成，另一个组件用Go写成。

## Protobuf

`Protobuf`全称`Protocol Buffer`，是 Google 公司于2008年开源的一种`语言无关`、`平台无关`、`可扩展的`用于`序列化结构化`数据——类似于XML，但比XML更小、更快、更简单，它可用于（数据）通信协议、数据存储等。你只需要定义一次你想要的数据结构，然后你就可以使用特殊生成的源代码来轻松地从各种数据流和各种语言中写入和读取你的结构化数据。目前 `Protobuf` 被广泛用作微服务中的通信协议。

- [protobuf v3语法官方文档](https://protobuf.dev/programming-guides/proto3/)
- [protobuf v3中文语法指南](https://liwenzhou.com/posts/Go/Protobuf3-language-guide-zh/)

### 标量值类型

| .proto Type |                                      Notes                                      | C++ Type | Java/Kotlin Type[1] | Python Type[3] |     Go Type      |     PHP Type      |
| :---------: | :-----------------------------------------------------------------------------: | :------: | :-----------------: | :------------: | :--------------: | :---------------: |
|   double    |                                                                                 |  double  |       double        |     float      |     float64      |       float       |
|    float    |                                                                                 |  float   |        float        |     float      |     float32      |       float       |
|    int32    | 使用可变长度编码。编码负数效率低下——如果你的字段可能有负值，则使用 sint32代替。 |  int32   |         int         |      int       |      int32       |      integer      |
|    int64    | 使用可变长度编码。编码负数效率低下——如果你的字段可能有负值，则使用 sint64代替。 |  int64   |        long         |  int/long[4]   |      int64       | integer/string[6] |
|   uint32    |                                 使用变长编码。                                  |  uint32  |       int[2]        |  int/long[4]   |      uint32      |      integer      |
|   uint64    |                                 使用变长编码。                                  |  uint64  |       long[2]       |  int/long[4]   |      uint64      | integer/string[6] |
|   sint32    |   使用可变长度编码。带符号的 int 值。这些编码比普通的 int32更有效地编码负数。   |  int32   |         int         |      int       |      int32       |      integer      |
|   sint64    |   使用可变长度编码。带符号的 int 值。这些编码比普通的 int64更有效地编码负数。   |  int64   |        long         |  int/long[4]   |      int64       | integer/string[6] |
|   fixed32   |             总是四个字节。如果值经常大于228，则比 uint32更有效率。              |  uint32  |       int[2]        |  int/long[4]   |      uint32      |      integer      |
|   fixed64   |               总是8字节。如果值经常大于256，则比 uint64更有效率。               |  uint64  |  integer/string[6]  |
|  sfixed32   |                                 总是四个字节。                                  |  int32   |         int         |      int       |      int32       |      integer      |
|  sfixed64   |                                 总是八个字节。                                  |  int64   |  integer/string[6]  |
|    bool     |                                                                                 |   bool   |       boolean       |      bool      |       bool       |      boolean      |
|   string    |         字符串必须始终包含 UTF-8编码的或7位 ASCII 文本，且不能长于232。         |  string  |       String        | str/unicode[5] |      string      |      string       |
|    bytes    |                    可以包含任何不超过232字节的任意字节序列。                    |  string  |     ByteString      | str (Python 2) | bytes (Python 3) |      []byte       | string |


在使用 Protocol Buffer Encoding 对消息进行序列化时，可以了解有关这些类型如何编码的更多信息。

- [1] Kotlin 使用来自 Java 的相应类型，甚至是无符号类型，以确保混合 Java/Kotlin 代码库的兼容性。
- [2] 在 Java 中，无符号的32位和64位整数使用它们的有符号对应项来表示，最高位存储在有符号位中。
- [3] 在任何情况下，为字段设置值都将执行类型检查，以确保其有效。
- [4] 64位或无符号的32位整数在解码时总是表示为 long ，但如果在设置字段时给出 int，则可以表示为 int。在任何情况下，值必须与设置时表示的类型相匹配。见[2]。
- [5] Python 字符串在解码时表示为 unicode，但如果给出了 ASCII 字符串，则可以表示为 str (这可能会更改)。
- [6] 整数用于64位机器，字符串用于32位机器。


### protobuf语法
```protobuf
syntax = "proto3"; // 版本声明，使用Protocol Buffers v3版本

option go_package = "xx";  // 指定生成的Go代码在你项目中的导入路径

package pb; // 包名


// 定义服务
service Greeter {
    // SayHello 方法
    rpc SayHello (HelloRequest) returns (HelloResponse) {}
    // ... 其他方法
}

// 请求消息
message HelloRequest {
    string name = 1;
}

// 响应消息
message HelloResponse {
    string reply = 1;
}

```


### 在gRPC中你可以定义四种类型的服务方法。

 - `普通 rpc`，客户端向服务器发送一个请求，然后得到一个响应，就像普通的函数调用一样。

    ```protobuf
    rpc SayHello(HelloRequest) returns (HelloResponse);
    ```

 - `服务器流式 rpc`，其中客户端向服务器发送请求，并获得一个流来读取一系列消息。客户端从返回的流中读取，直到没有更多的消息。gRPC 保证在单个 RPC 调用中的消息是有序的。
    ```protobuf
    rpc LotsOfReplies(HelloRequest) returns (stream HelloResponse);
    ```

 - `客户端流式 rpc`，其中客户端写入一系列消息并将其发送到服务器，同样使用提供的流。一旦客户端完成了消息的写入，它就等待服务器读取消息并返回响应。同样，gRPC 保证在单个 RPC 调用中对消息进行排序。
    ```protobuf
    rpc LotsOfGreetings(stream HelloRequest) returns (HelloResponse);
    ```

 - `双向流式 rpc`，其中双方使用读写流发送一系列消息。这两个流独立运行，因此客户端和服务器可以按照自己喜欢的顺序读写: 例如，服务器可以等待接收所有客户端消息后再写响应，或者可以交替读取消息然后写入消息，或者其他读写组合。每个流中的消息是有序的。
    ```protobuf
    rpc LotsOfGreetings(stream HelloRequest) returns (stream HelloResponse);
    ```


## 安装

### 安装Protocol Buffers v3

安装用于生成gRPC服务代码的协议编译器，最简单的方法是从下面的链接：https://github.com/google/protobuf/releases 下载适合你平台的预编译好的二进制文件（protoc-<version>-<platform>.zip）。

其中解压之后的文件：

- bin 目录下的 protoc 是可执行文件。
- include 目录下的是 google 定义的.proto文件，我们`import "google/protobuf/timestamp.proto"`就是从此处导入。

!> 我们需要将下载得到的可执行文件protoc所在的 bin 目录加到我们电脑的`环境变量`中。

### 安装插件

因为本文我们是使用Go语言做开发，接下来执行下面的命令安装protoc的Go插件：

#### 安装go语言插件：
```terminal
go install google.golang.org/protobuf/cmd/protoc-gen-go
```

该插件会根据.proto文件生成一个后缀为.pb.go的文件，包含所有.proto文件中定义的类型及其序列化方法。

#### 安装grpc插件：

```terminal
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc
```

该插件会生成一个后缀为_grpc.pb.go的文件，其中包含：

- 一种接口类型(或存根) ，供客户端调用的服务方法。
- 服务器要实现的接口类型。

> 上述命令会默认将插件安装到`$GOPATH/bin`，为了protoc编译器能找到这些插件，请确保你的`$GOPATH/bin`在环境变量中。

## 验证安装

依次执行以下命令检查一下是否开发环境都准备完毕。

- 确认 protoc 安装完成。
```terminal
❯ protoc --version
libprotoc 3.20.1
```

- 确认 protoc-gen-go 安装完成。
```terminal
❯ protoc-gen-go --version
protoc-gen-go v1.28.0
```
> 如果这里提示protoc-gen-go不是可执行的程序，请确保你的 GOPATH 下的 bin 目录在你电脑的环境变量中。

- 确认 protoc-gen-go-grpc 安装完成。
```terminal
❯ protoc-gen-go-grpc --version
protoc-gen-go-grpc 1.2.0
```
> 如果这里提示protoc-gen-go-grpc不是可执行的程序，请确保你的 GOPATH 下的 bin 目录在你电脑的环境变量中。
 