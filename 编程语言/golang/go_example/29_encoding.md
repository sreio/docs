## base32

```go
package main

import (
	"bytes"
	"encoding/base32"
	"fmt"
	"io/ioutil"
	"log"
)

// 实现了base32编码
func main() {

	// 声明内容
	var origin = []byte("Hello World!")
	// 声明buffer
	var buf bytes.Buffer
	// 自定一个32字节的字符串
	var customEncode = "ABCDEFGHIJKLMNOPQRSTUVWXYZ234567"
	// 使用给出的字符集生成一个*base32.Encoding，字符集必须是32字节的字符串
	e := base32.NewEncoding(customEncode)
	// 创建一个新的base32流编码器
	w := base32.NewEncoder(e, &buf)
	// 写入
	if _, err := w.Write(origin); err != nil {
		log.Fatal(err)
	}
	// 关闭
	if err := w.Close(); err != nil {
		log.Fatal(err)
	}
	fmt.Println("base32编码内容: ", string(buf.Bytes()))

	// 创建一个新的base32流解码器
	r := base32.NewDecoder(base32.StdEncoding, &buf)
	// 读取内容
	b, err := ioutil.ReadAll(r)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("base32解码内容: ", string(b))



	// 使用标准的base32编码字符集编码
	originEncode := base32.StdEncoding.EncodeToString(origin)
	fmt.Println("base32编码内容: ", originEncode)

	// 使用标准的base32编码字符集解码
	originBytes, err := base32.StdEncoding.DecodeString(originEncode)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("base32解码内容: ", string(originBytes))



	// 获取数据进行base32编码后的最大长度
	var ne = base32.StdEncoding.EncodedLen(len(origin))
	// 声明[]byte
	var dst = make([]byte, ne)
	// 将src的数据编码后存入dst，最多写EncodedLen(len(src))字节数据到dst，并返回写入的字节数
	base32.StdEncoding.Encode(dst, origin)
	fmt.Println("base32编码内容: ", string(dst))

	// 获取base32编码的数据解码后的最大长度
	var nd = base32.StdEncoding.DecodedLen(len(dst))
	// 声明[]byte
	var originText = make([]byte, nd)
	if _, err := base32.StdEncoding.Decode(originText, dst); err != nil {
		log.Fatal(err)
	}
	fmt.Println("base32解码内容: ", string(originText))

	// base32.HexEncoding 定义用于"扩展Hex字符集"，用于DNS
}
```

## base64

```go
package main

import (
	"bytes"
	"encoding/base64"
	"fmt"
	"io/ioutil"
	"log"
)

// 实现了base64编码
func main() {


	// 声明内容
	var origin = []byte("Hello World!")
	// 声明buffer
	var buf bytes.Buffer
	// 自定一个64字节的字符串
	var customEncode = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"
	// 使用给出的字符集生成一个*base64.Encoding，字符集必须是32字节的字符串
	e := base64.NewEncoding(customEncode)
	// 创建一个新的base64流编码器
	w := base64.NewEncoder(e, &buf)
	// 写入
	if _, err := w.Write(origin); err != nil {
		log.Fatal(err)
	}
	// 关闭
	if err := w.Close(); err != nil {
		log.Fatal(err)
	}
	fmt.Println("base64编码内容: ", string(buf.Bytes()))

	// 创建一个新的base64流解码器
	r := base64.NewDecoder(base64.StdEncoding, &buf)
	// 读取内容
	b, err := ioutil.ReadAll(r)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("base64解码内容: ", string(b))



	// 使用标准的base64编码字符集编码
	originEncode := base64.StdEncoding.EncodeToString(origin)
	fmt.Println("base64编码内容: ", originEncode)

	// 使用标准的base64编码字符集解码
	originBytes, err := base64.StdEncoding.DecodeString(originEncode)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("base64解码内容: ", string(originBytes))



	// 获取数据进行base64编码后的最大长度
	var ne = base64.StdEncoding.EncodedLen(len(origin))
	// 声明[]byte
	var dst = make([]byte, ne)
	// 将src的数据编码后存入dst，最多写EncodedLen(len(src))字节数据到dst，并返回写入的字节数
	base64.StdEncoding.Encode(dst, origin)
	fmt.Println("base64编码内容: ", string(dst))

	// 获取base64编码的数据解码后的最大长度
	var nd = base64.StdEncoding.DecodedLen(len(dst))
	// 声明[]byte
	var originText = make([]byte, nd)
	if _, err := base64.StdEncoding.Decode(originText, dst); err != nil {
		log.Fatal(err)
	}
	fmt.Println("base64解码内容: ", string(originText))

	// 创建与enc相同的新编码，指定的填充字符除外，或者nopadding禁用填充。填充字符不能是'\r'或'\n'，不能包含在编码的字母表中，并且必须是等于或小于'\xff'的rune
	base64.StdEncoding.WithPadding(base64.StdPadding)
	// base64.StdEncoding 定义标准base64编码字符集
	// base64.URLEncoding 定义用于URL和文件名的，base64编码字符集
	// base64.RawStdEncoding 定义标准无填充字符的base64编码字符集
	// base64.RawURLEncoding 定义用于URL和文件名的，无填充字符的base64编码字符集
}
```


## json

```go
package main

import (
	"bytes"
	"encoding/json"
	"fmt"
	"log"
)

// json包实现了json对象的编解码
func main() {

	// 声明内容
	var data = []byte(`{"message": "Hello Gopher!"}`)
	// 验证内容是否符合json格式
	json.Valid(data)

	example()
	example2()
}

type Hello struct {
	Name string
	Sex  int
}

func example() {

	// 声明一个结构体
	hello := Hello{"Gopher", 1}
	// 进行json编码
	b, err := json.Marshal(hello)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(b))

	// 声明一个空的结构体
	originHello := Hello{}
	// 将json内容解码到指定结构中
	if err := json.Unmarshal(b, &originHello); err != nil {
		log.Fatal(err)
	}
	fmt.Println(originHello)
}

func example2() {

	// 声明buffer
	var buf bytes.Buffer
	// 声明一个结构体
	hello := Hello{"<div>World</div>", 2}

	// 创建一个将数据写入w的*Encoder
	e := json.NewEncoder(&buf)

	// 是否转义html标签, 例如：&, <, > 为\u0026, \u003c, \u003e
	e.SetEscapeHTML(false)

	// 设置缩进(可以随意设置缩进内容)
	e.SetIndent("", "")

	// 进行json编码
	if err := e.Encode(hello); err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(buf.Bytes()))

	// 声明一个空的结构体
	originHello := Hello{}

	// 创建一个从r读取并解码json对象的*Decoder
	d := json.NewDecoder(&buf)

	// 返回保存在json.Decoder缓存里数据的读取器，该返回值在下次调用Decode方法之前有效
	d.Buffered()

	// 设置此项，如果发现不能识别的字符串将导致解码失败
	d.DisallowUnknownFields()

	// 设置此项，当接收端是interface{}接口时将json数字解码为Number类型而不是float64类型
	d.UseNumber()

	// 返回是否存在其他元素
	d.More()

	// 将json内容解码到指定结构中
	if err := d.Decode(&originHello); err != nil {
		log.Fatal(err)
	}
	fmt.Println(originHello)

}
```

## csv

```go
package main

import (
	"bytes"
	"encoding/csv"
	"fmt"
	"io"
	"log"
)

// csv读写逗号分隔值（csv）的文件
func main() {

	var data = []string{"test", "Hello", "Go"}

	var buf bytes.Buffer

	// 初始化一个writer
	w := csv.NewWriter(&buf)
	// 写入
	if err := w.Write(data); err != nil {
		log.Fatal(err)
	}
	// 将缓存中的数据写入底层的io.Writer。要检查Flush时是否发生错误的话，应调用Error
	w.Flush()
	// 抓取错误信息
	if err := w.Error(); err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(buf.Bytes()))


	// 初始化一个reader
	r := csv.NewReader(&buf)
	for {
		// 从r读取一条记录，返回值record是字符串的切片，每个字符串代表一个字段
		record, err := r.Read()
		if err == io.EOF { // 判断是否结尾
			break
		}
		if err != nil {
			log.Fatal(err)
		}
		fmt.Println(record)
	}

	// 从r中读取所有剩余的记录，每个记录都是字段的切片
	// 成功的调用返回值err为nil而不是EOF,因为ReadAll方法定义为读取直到文件结尾，因此它不会将文件结尾视为应该报告的错误
	// *读取过的记录不会再次被读取
	records, err := r.ReadAll()
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(records)
}
```


## binary

```go
package main

import (
	"bytes"
	"encoding/binary"
	"fmt"
	"log"
	"math"
)

// binary包实现了简单的数字与byte的转换以及变长值的编解
// 数字翻译为定长值来读写，一个定长值，要么是固定长度的数字类型（int8, uint8, int16, float32, complex64, ...）或者只包含定长值的结构体或者数组
// 本包相对于效率更注重简单。如果需要高效的序列化，特别是数据结构较复杂的，请参见更高级的解决方法，例如encoding/gob包，或者采用协议缓存
// 简述：可用于 将uint，float，complex等类型与[]byte类型互相转换
func main() {

	// 编码/解码
	example()
	// 多个数字一起编码/解码
	multiExample()

	ByteOrder()
	uvarint()
}

func example() {

	// 指定binary写入时的字节序
	// binary.BigEndian 大端字节序的实现
	// binary.LittleEndian 小端字节序的实现
	var order = binary.LittleEndian
	// 声明内容
	var data = math.Pi
	// 初始化一个buffer
	buf := new(bytes.Buffer)

	// 将data的binary编码格式写入w，data必须是定长值、定长值的切片、定长值的指针
	// order指定写入数据的字节序，写入结构体时，名字中有'_'的字段会置为0
	if err := binary.Write(buf, order, data); err != nil {
		log.Fatal("binary.Write failed:", err)
	}
	fmt.Printf("%x\n", buf.Bytes())


	var origin float64
	// 从r中读取binary编码的数据并赋给data，data必须是一个指向定长值的指针或者定长值的切片
	// 从r读取的字节使用order指定的字节序解码并写入data的字段里当写入结构体是，名字中有'_'的字段会被跳过，这些字段可用于填充（内存空间）
	if err := binary.Read(buf, order, &origin); err != nil {
		log.Fatal("binary.Read failed:", err)
	}
	fmt.Println(origin)
}

func multiExample() {

	// 初始化buffer
	buf := new(bytes.Buffer)
	// 声明interface{}切片
	var data = []interface{}{
		uint16(61374),
		int8(-54),
		uint8(254),
	}
	// 循环切片按顺序编码写入buf
	for _, v := range data {
		if err := binary.Write(buf, binary.LittleEndian, v); err != nil {
			log.Fatal("binary.Write failed:", err)
		}
	}
	fmt.Printf("%x\n", buf.Bytes())

	var origin = struct {
		A uint16
		B int8
		C uint8
	}{}

	// 解码
	if err := binary.Read(buf, binary.LittleEndian, &origin); err != nil {
		log.Fatal("binary.Read failed:", err)
	}
	fmt.Println(origin)
}

func ByteOrder() {

	// 声明一个长度为4的[]byte
	b := make([]byte, 4)
	// 将16比特的无符号整数 转为 字节序列
	binary.LittleEndian.PutUint16(b[:2], '1')
	binary.LittleEndian.PutUint16(b[2:], 0x07d0)
	fmt.Printf("% x\n", b)

	// 将字节序列 转为 16比特的无符号整数
	x1 := binary.LittleEndian.Uint16(b[:2])
	x2 := binary.LittleEndian.Uint16(b[2:])
	fmt.Printf("%#04x %#04x\n", x1, x2)
}

func uvarint() {

	var n uint64 = 256

	// 根据 变长编码N位整数的最大字节数 初始化一个[]byte
	buf := make([]byte, binary.MaxVarintLen64)
	// 将一个uint64数字编码写入buf并返回写入的长度，如果buf太小，则会panic
	binary.PutUvarint(buf, n)

	// 从reader读取一个编码后的无符号整数，并返回该整数
	i , err := binary.ReadUvarint(bytes.NewReader(buf))
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(i)

	// binary.PutVarint binary.ReadVarint 同上
}
```

## hex

```go
package main

import (
	"bytes"
	"encoding/hex"
	"fmt"
	"io/ioutil"
	"log"
)

// hex包实现了16进制字符表示的编解码
func main() {

	// 编码/解码
	example()
	example2()

	// 编码/解码(string)
	exampleString()

	// hex dump编码
	exampleDump()
}

func example() {

	// 声明内容
	var src = []byte("Hello Gopher!")

	// 声明一个 明文数据编码后的编码数据的长度 的[]byte
	dst := make([]byte, hex.EncodedLen(len(src)))

	// 将src的数据解码为EncodedLen(len(src))字节，返回实际写入dst的字节数
	hex.Encode(dst, src)
	fmt.Printf("%s\n", dst)

	// 声明一个 编码数据解码后的明文数据的长度 的[]byte
	origin := make([]byte, hex.DecodedLen(len(dst)))
	// 将src解码为DecodedLen(len(src))字节，返回实际写入dst的字节数；如遇到非法字符，返回描述错误的error
	_, err := hex.Decode(origin, dst)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Printf("%s\n", origin)
}

func example2() {

	// 声明内容
	var src = []byte("Hello Gopher!")
	// 声明buffer
	var buf bytes.Buffer

	// 创建一个用于编码的writer
	w := hex.NewEncoder(&buf)
	if _, err := w.Write(src); err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(buf.Bytes()))

	// 创建一个用于解码的reader
	r := hex.NewDecoder(&buf)
	// 读取内容
	b, err := ioutil.ReadAll(r)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(b))
}

func exampleString() {

	// 声明内容
	var src = []byte("Hello Gopher!")

	// 将数据src编码为字符串
	str := hex.EncodeToString(src)
	fmt.Println(str)

	// 将十六进制字符串解码为原数据
	origin, err := hex.DecodeString(str)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(origin))
}

func exampleDump() {

	// 声明内容
	content := []byte("Go is an open source programming language.")

	// 返回回给定数据的hex dump格式的字符串，这个字符串与控制台下`hexdump -C`对该数据的输出是一致的
	str := hex.Dump(content)
	fmt.Printf("%s\n", str)



	// 声明buffer
	var buf bytes.Buffer
	// 返回一个io.WriteCloser接口，将写入的数据的hex dump格式写入w，具体格式为'hexdump -C'
	dumper := hex.Dumper(&buf)
	// 关闭
	defer dumper.Close()
	// 写入
	if _, err := dumper.Write(content); err != nil {
		log.Fatal(err)
	}
	fmt.Printf("%s\n", buf.Bytes())
}
```


## xml 

```go
package main

import (
	"bytes"
	"encoding/xml"
	"fmt"
	"log"
)

// xml包实现了XML 1.0解析
func main() {

	example()
	example2()
}

type Hello struct {
	XMLName   xml.Name `xml:"xml"`        // 指定xml头标签,不指定默认为结构体名称
	Id        int      `xml:"id,attr"`    // 指定xml头标签上的元素
	FullName  string   `xml:"fullName"`   // 指定标签名
	FirstName string   `xml:"name>first"` // 指定上级标签
	LastName  string   `xml:"name>last"`  // 指定上级标签
	Sex       int      `xml:"sex"`        // 指定标签名
	Comment   string   `xml:",comment"`   // 声明注释
}

func example() {

	// 声明结构体
	hello := Hello{
		Id:        12,
		FullName:  "zc",
		FirstName: "z",
		LastName:  "c",
		Sex:       1,
		Comment:   "test notes",
	}

	// xml编码
	b, err := xml.Marshal(hello)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(b))

	// 声明一个空的结构体
	origin := Hello{}
	if err := xml.Unmarshal(b, &origin); err != nil {
		log.Fatal(err)
	}
	fmt.Println(origin)

	// 自定义前缀和index编码
	bi, err := xml.MarshalIndent(hello, "\r", "  ")
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(bi))
}

func example2() {

	// 声明buffer
	var buf bytes.Buffer
	// 声明结构体
	hello := Hello{
		Id:        12,
		FullName:  "zc",
		FirstName: "z",
		LastName:  "c",
		Sex:       1,
		Comment:   "test notes",
	}

	// 创建一个写入w的*Encoder
	e := xml.NewEncoder(&buf)

	// 自定义前缀和index
	e.Indent("\r", "  ")

	// 自定义xml头编码
	err := e.EncodeElement(hello, xml.StartElement{
		Name: xml.Name{
			Space: "test",   // xmlns
			Local: "newXml", // xml头名称
		},
		Attr: nil, // 设置xml头元素
	})
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(buf.Bytes()))
}
```