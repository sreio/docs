## bzip2

```go
package main

import (
	"bytes"
	"compress/bzip2"
	"fmt"
	"io/ioutil"
	"log"
)

const FilePath = "testdata/test.bz2"

// bzip2包实现bzip2的解压缩
func main() {

	// 读取文件内容
	bf, err := ioutil.ReadFile(FilePath)
	if err != nil {
		log.Fatal(err)
	}

	// bzip2解压缩成reader
	r := bzip2.NewReader(bytes.NewReader(bf))

	// 读取内容
	b, err := ioutil.ReadAll(r)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println("bzip2 content: ", string(b))
}
```

## flate
```go
package main

import (
	"bytes"
	"compress/flate"
	"fmt"
	"io/ioutil"
	"log"
	"os"
)

const FilePath = "testdata/test.deflate"
const DictFilePath = "testdata/testDict.deflate"

// flate包实现了deflate压缩数据格式。gzip包和zlib包实现了对基于deflate的文件格式的访问
func main() {

	// 写deflate数据流
	buf := write()
	// 自动生成并写入文件
	if err := ioutil.WriteFile(FilePath, buf.Bytes(), os.ModePerm); err != nil {
		log.Fatal(err)
	}
	// 读deflate数据流
	read(FilePath)

	const dict = `<?xml version="1.0"?><book></book><data></data><meta name="" content="`
	// 预设字典写deflate数据流
	dictBuf := writeDict(dict)
	// 自动生成并写入文件
	if err := ioutil.WriteFile(DictFilePath, dictBuf.Bytes(), os.ModePerm); err != nil {
		log.Fatal(err)
	}
	// 根据预设字典读deflate数据流
	readDict(dict, DictFilePath)
}

func write() bytes.Buffer {

	inputs := []string{
		"Don't communicate by sharing memory, share memory by communicating.\n",
		"Concurrency is not parallelism.\n",
		"The bigger the interface, the weaker the abstraction.\n",
		"Documentation is for users.\n",
	}
	resetInput := "This is a reset test message!"

	var buf bytes.Buffer

	// 初始化writer，可设置压缩类型
	fw, err := flate.NewWriter(&buf, flate.DefaultCompression)
	if err != nil {
		log.Fatal(err)
	}

	for _, v := range inputs {
		// 写入
		if _, err := fw.Write([]byte(v)); err != nil {
			log.Fatal(err)
		}
		// 挂起写入数据，重置缓冲区
		if err := fw.Flush(); err != nil {
			log.Fatal(err)
		}
	}

	// 重置buffer内容为空
	fw.Reset(&buf)
	fw.Write([]byte(resetInput))

	// 关闭writer
	if err := fw.Close(); err != nil {
		log.Fatal(err)
	}
	return buf
}

func read(path string) {

	// 读取文件内容
	bf, err := ioutil.ReadFile(path)
	if err != nil {
		log.Fatal(err)
	}

	// 初始化reader
	r := flate.NewReader(bytes.NewReader(bf))
	if err := r.Close(); err != nil {
		log.Fatal(err)
	}

	// 读取内容
	b, err := ioutil.ReadAll(r)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(b))
}

func writeDict(dict string) bytes.Buffer {

	const data = `<?xml version="1.0"?>
<book>
	<meta name="title" content="The Go Programming Language"/>
	<meta name="authors" content="Alan Donovan and Brian Kernighan"/>
	<meta name="published" content="2015-10-26"/>
	<meta name="isbn" content="978-0134190440"/>
	<data>...</data>
</book>`

	var buf bytes.Buffer

	// 初始化一个预设字典的writer
	fw, err := flate.NewWriterDict(&buf, flate.DefaultCompression, []byte(dict))
	defer fw.Close()
	if err != nil {
		log.Fatal(err)
	}

	// 写入
	if _, err := fw.Write([]byte(data)); err != nil {
		log.Fatal(err)
	}
	// 关闭
	if err := fw.Close(); err != nil {
		log.Fatal(err)
	}
	return buf
}

func readDict(dict string, path string) {

	bf, err := ioutil.ReadFile(path)
	if err != nil {
		log.Fatal(err)
	}

	// 可以读取的时候替换字典内容，但是必须保证长度相同，注释测试原字典解压
	hashDict := []byte(dict)
	for i := range hashDict {
		hashDict[i] = '#'
	}

	// 初始化一个预设字典的reader
	fr := flate.NewReaderDict(bytes.NewReader(bf), hashDict)
	defer fr.Close()

	b, err := ioutil.ReadAll(fr)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(b))
}
```

## gzip
```go
package main

import (
	"bytes"
	"compress/flate"
	"compress/gzip"
	"fmt"
	"io"
	"io/ioutil"
	"log"
	"os"
	"time"
)

const FilePath = "testdata/test.gzip"

// gzip包实现了gzip格式压缩文件的读写
func main() {

	// 写gzip数据流
	buf := write()
	// 自动生成并写入文件
	if err := ioutil.WriteFile(FilePath, buf.Bytes(), os.ModePerm); err != nil {
		log.Fatal(err)
	}
	// 读gzip数据流
	read(FilePath)
}

func write() bytes.Buffer {

	var files = []struct {
		name    string
		comment string
		modTime time.Time
		data    string
	}{
		{"file-1.txt", "file-header-1", time.Now(), "Hello Gophers - 1"},
		{"file-2.txt", "file-header-2", time.Now(), "Hello Gophers - 2"},
	}

	// 声明buffer
	var buf bytes.Buffer

	// 初始化writer
	gw := gzip.NewWriter(&buf)

	// 初始化writer，可设置压缩级别
	gw, err := gzip.NewWriterLevel(&buf, flate.BestCompression)
	if err != nil {
		log.Fatal(err)
	}

	for _, file := range files {
		gw.Name = file.name       // 设置文件名称
		gw.Comment = file.comment // 设置说明
		gw.ModTime = file.modTime // 设置修改时间
		gw.Extra = []byte("")     // 设置额外内容
		// 写入
		if _, err := gw.Write([]byte(file.data)); err != nil {
			log.Fatal(err)
		}

		// 关闭
		if err := gw.Close(); err != nil {
			log.Fatal(err)
		}

		// 重置buffer内容
		gw.Reset(&buf)
	}

	return buf
}

func read(path string) {

	// 读取文件内容
	bf, err := ioutil.ReadFile(path)
	if err != nil {
		log.Fatal(err)
	}

	buf := bytes.NewReader(bf)
	// 初始化reader
	gr, err := gzip.NewReader(buf)
	defer gr.Close()
	if err != nil {
		log.Fatal(err)
	}

	for {
		// 设置数据流文件内容分隔，false分开每个单独文件循环读取reader内容。true所有文件内容一次性读取
		gr.Multistream(false)

		b, err := ioutil.ReadAll(gr)
		if err != nil {
			log.Fatal(err)
		}
		fmt.Printf("Name: %s\nComment: %s\nModTime: %s\n", gr.Name, gr.Comment, gr.ModTime.UTC())
		fmt.Println(string(b))

		err = gr.Reset(buf)
		if err == io.EOF {
			break
		}
		if err != nil {
			log.Fatal(err)
		}
	}
}
```

## lzw

```go
package main

import (
	"bytes"
	"compress/lzw"
	"fmt"
	"io/ioutil"
	"log"
	"os"
)

const FilePath = "testdata/test.lzw"

// lzw包实现了Lempel-Ziv-Welch数据压缩格式，这是一种T. A. Welch在“A Technique for High-Performance Data Compression”一文（Computer, 17(6) (June 1984), pp 8-19）提出的一种压缩格式
// 本包实现了用于GIF、TIFF、PDF文件的lzw压缩格式，这是一种最长达到12位的变长码，头两个非字面码为clear和EOF码
func main() {

	// 写lzw数据流
	buf := write()
	// 自动生成并写入文件
	if err := ioutil.WriteFile(FilePath, buf.Bytes(), os.ModePerm); err != nil {
		log.Fatal(err)
	}
	// 读lzw数据流
	read(FilePath)
}

func write() bytes.Buffer {

	var input = "Hello World!"

	var buf bytes.Buffer

	// lzw: Lempel-Ziv-Welch数据压缩格式
	// 初始化writer
	// lsb表示最低有效位，在gif文件格式中使用。
	// msb表示最高有效位，在tiff和pdf中所用
	// litWidth编码位数，范围[2,8]且通常为8。输入字节必须小于1<<litwidth。
	lw := lzw.NewWriter(&buf, lzw.LSB, 8)

	// 写入
	if _, err := lw.Write([]byte(input)); err != nil {
		log.Fatal(err)
	}

	// 关闭
	if err := lw.Close(); err != nil {
		log.Fatal(err)
	}
	return buf
}

func read(path string) {

	// 读取文件内容
	bf, err := ioutil.ReadFile(path)
	if err != nil {
		log.Fatal(err)
	}

	// 初始化reader
	lr := lzw.NewReader(bytes.NewReader(bf), lzw.LSB, 8)
	defer lr.Close()

	// 读取内容
	b, err := ioutil.ReadAll(lr)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(b))
}
```

## zlib

```go
package main

import (
	"bytes"
	"compress/lzw"
	"fmt"
	"io/ioutil"
	"log"
	"os"
)

const FilePath = "testdata/test.lzw"

// lzw包实现了Lempel-Ziv-Welch数据压缩格式，这是一种T. A. Welch在“A Technique for High-Performance Data Compression”一文（Computer, 17(6) (June 1984), pp 8-19）提出的一种压缩格式
// 本包实现了用于GIF、TIFF、PDF文件的lzw压缩格式，这是一种最长达到12位的变长码，头两个非字面码为clear和EOF码
func main() {

	// 写lzw数据流
	buf := write()
	// 自动生成并写入文件
	if err := ioutil.WriteFile(FilePath, buf.Bytes(), os.ModePerm); err != nil {
		log.Fatal(err)
	}
	// 读lzw数据流
	read(FilePath)
}

func write() bytes.Buffer {

	var input = "Hello World!"

	var buf bytes.Buffer

	// lzw: Lempel-Ziv-Welch数据压缩格式
	// 初始化writer
	// lsb表示最低有效位，在gif文件格式中使用。
	// msb表示最高有效位，在tiff和pdf中所用
	// litWidth编码位数，范围[2,8]且通常为8。输入字节必须小于1<<litwidth。
	lw := lzw.NewWriter(&buf, lzw.LSB, 8)

	// 写入
	if _, err := lw.Write([]byte(input)); err != nil {
		log.Fatal(err)
	}

	// 关闭
	if err := lw.Close(); err != nil {
		log.Fatal(err)
	}
	return buf
}

func read(path string) {

	// 读取文件内容
	bf, err := ioutil.ReadFile(path)
	if err != nil {
		log.Fatal(err)
	}

	// 初始化reader
	lr := lzw.NewReader(bytes.NewReader(bf), lzw.LSB, 8)
	defer lr.Close()

	// 读取内容
	b, err := ioutil.ReadAll(lr)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(b))
}
```