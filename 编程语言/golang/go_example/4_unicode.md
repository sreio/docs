
## Unicode

```go
package main

import (
	"fmt"
	"unicode"
)

// unicode包提供数据和函数来测试Unicode代码点的一些属性
func main() {

	// 判断示例
	exampleIs()
	// 对应示例
	exampleSimpleFold()
	// 转换示例
	exampleTo()
}

func exampleIs() {

	// constant with mixed type runes
	const mixed = "\b5Ὂg̀9! ℃ᾭG"
	for _, c := range mixed {

		fmt.Printf("For %q:\n", c)

		// 判断一个字符是否是控制字符，主要是策略C的字符和一些其他的字符如代理字符
		if unicode.IsControl(c) {
			fmt.Println("\tis control rune")
		}

		// 判断一个r字符是否是十进制数字字符
		if unicode.IsDigit(c) {
			fmt.Println("\tis digit rune")
		}

		// 判断一个字符是否是unicode图形。包括字母、标记、数字、符号、标点、空白，参见L、M、N、P、S、Zs
		if unicode.IsGraphic(c) {
			fmt.Println("\tis graphic rune")
		}

		// 判断一个字符是否是字母
		if unicode.IsLetter(c) {
			fmt.Println("\tis letter rune")
		}

		// 判断字符是否是小写字母
		if unicode.IsLower(c) {
			fmt.Println("\tis lower case rune")
		}

		// 判断字符是否是大写字母
		if unicode.IsUpper(c) {
			fmt.Println("\tis upper case rune")
		}

		// 判断一个字符是否是标记字符
		if unicode.IsMark(c) {
			fmt.Println("\tis mark rune")
		}

		// 判断一个字符是否是数字字符
		if unicode.IsNumber(c) {
			fmt.Println("\tis number rune")
		}

		// 判断一个字符是否是go的可打印字符
		// 本函数基本和IsGraphic一致，只是ASCII空白字符U+0020会返回假
		if unicode.IsPrint(c) {
			fmt.Println("\tis printable rune")
		}

		// 判断一个字符是否是unicode标点字符
		if unicode.IsPunct(c) {
			fmt.Println("\tis punct rune")
		}

		// 判断一个字符是否是空白字符
		// 在Latin-1字符空间中，空白字符为：'\t', '\n', '\v', '\f', '\r', ' ', U+0085 (NEL), U+00A0 (NBSP).其它的空白字符请参见策略Z和属性Pattern_White_Space
		if unicode.IsSpace(c) {
			fmt.Println("\tis space rune")
		}

		// 判断一个字符是否是unicode符号字符
		if unicode.IsSymbol(c) {
			fmt.Println("\tis symbol rune")
		}

		// 判断字符是否是标题字母
		if unicode.IsTitle(c) {
			fmt.Println("\tis title case rune")
		}
	}
}

func exampleSimpleFold() {

	// 迭代在unicode标准字符映射中互相对应的unicode码值
	// 在与r对应的码值中（包括r自身），会返回最小的那个大于r的字符（如果有）；否则返回映射中最小的字符
	fmt.Printf("%#U\n", unicode.SimpleFold('A'))      // 'a'
	fmt.Printf("%#U\n", unicode.SimpleFold('a'))      // 'A'
	fmt.Printf("%#U\n", unicode.SimpleFold('K'))      // 'k'
	fmt.Printf("%#U\n", unicode.SimpleFold('k'))      // '\u212A' (Kelvin symbol, K)
	fmt.Printf("%#U\n", unicode.SimpleFold('\u212A')) // 'K'
	fmt.Printf("%#U\n", unicode.SimpleFold('1'))      // '1'
}

func exampleTo() {

	const lcG = 'g'

	// 转大写
	fmt.Printf("%#U\n", unicode.To(unicode.UpperCase, lcG))
	fmt.Println(unicode.ToUpper(lcG))
	// 转小写
	fmt.Printf("%#U\n", unicode.To(unicode.LowerCase, lcG))
	fmt.Println(unicode.ToLower(lcG))
	// 转标题
	fmt.Printf("%#U\n", unicode.To(unicode.TitleCase, lcG))
	fmt.Println(unicode.ToTitle(lcG))
}
```


## unicode/utf8

```go
package main

import (
	"unicode/utf8"
)

// utf8包实现了对utf-8文本的常用函数和常数的支持，包括rune和utf-8编码byte序列之间互相翻译的函数
func main() {

	var b = []byte("Hello World!")

	// 判断切片p是否包含完整且合法的utf-8编码序列
	utf8.Valid(b)

	// 判断r是否可以编码为合法的utf-8序列
	utf8.ValidRune('H')

	// 判断s是否包含完整且合法的utf-8编码序列
	utf8.ValidString(string(b))

	// 将r的utf-8编码序列写入p（p必须有足够的长度），并返回写入的字节数
	utf8.EncodeRune(b, 'H')

	// 解码p开始位置的第一个utf-8编码的码值，返回该码值和编码的字节数
	// 如果编码不合法，会返回(RuneError, 1)。该返回值在正确的utf-8编码情况下是不可能返回的
	utf8.DecodeRune(b)

	// 类似DecodeRune但输入参数是字符串
	utf8.DecodeRuneInString(string(b))

	// 解码p中最后一个utf-8编码序列，返回该码值和编码序列的长度
	utf8.DecodeLastRune(b)

	// 类似DecodeLastRune但输入参数是字符串
	utf8.DecodeLastRuneInString(string(b))

	// 判断切片p是否以一个码值的完整utf-8编码开始
	// 不合法的编码因为会被转换为宽度1的错误码值而被视为完整的
	// 如中文字符占3位byte，一位byte判断为false，完整的3位为true
	utf8.FullRune(b)

	// 类似FullRune但输入参数是字符串
	utf8.FullRuneInString(string(b))

	// 返回p中的utf-8编码的码值的个数。错误或者不完整的编码会被视为宽度1字节的单个码值
	utf8.RuneCount(b)

	// 类似RuneCount但输入参数是一个字符串
	utf8.RuneCountInString(string(b))

	// 返回r编码后的字节数。如果r不是一个合法的可编码为utf-8序列的值，会返回-1
	utf8.RuneLen('世')

	// 判断字节b是否可以作为某个rune编码后的第一个字节。第二个即之后的字节总是将左端两个字位设为10
	utf8.RuneStart('世')
}
```

## unicode/utf16

```go
package main

import "unicode/utf16"

// utf16包实现了UTF-16序列的编解码
func main() {

	var b = []rune("Hello World!")

	// 判断r是否可以编码为一个utf16的代理对
	utf16.IsSurrogate('H')

	// 将unicode码值序列编码为utf-16序列
	e := utf16.Encode(b)

	// 将utf-16序列解码为unicode码值序列
	utf16.Decode(e)

	// 将unicode码值r编码为一个utf-16的代理对。如果不能编码，会返回(U+FFFD, U+FFFD)
	r1, r2 := utf16.EncodeRune('H')

	// 将utf-16代理对(r1, r2)解码为unicode码值。如果代理对不合法，会返回U+FFFD
	utf16.DecodeRune(r1, r2)
}
```
