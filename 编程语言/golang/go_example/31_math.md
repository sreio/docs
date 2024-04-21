## math

```go
package main

import (
	"math"
)

// math包提供了基本的数学常数和数学函数
func main() {

	// 返回一个NaN, IEEE 754“这不是一个数字”值
	math.NaN()

	// 判断f是否表示一个NaN（Not A Number）值
	math.IsNaN(12.34)

	// 如果sign>=0函数返回正无穷大，否则返回负无穷大
	f := math.Inf(0)

	// 如果sign > 0，f是正无穷大时返回真；如果sign<0，f是负无穷大时返回真；sign==0则f是两种无穷大时都返回真
	math.IsInf(12.34, 1)
	math.IsInf(f, 0)

	// 返回浮点数f的IEEE 754格式二进制表示对应的4字节无符号整数
	// float32转uint32
	u32 := math.Float32bits(12.34)

	// 返回无符号整数b对应的IEEE 754格式二进制表示的4字节浮点数
	// uint32转float32
	math.Float32frombits(u32)

	// 返回浮点数f的IEEE 754格式二进制表示对应的8字节无符号整数
	// float64转uint64
	u64 := math.Float64bits(12.34)

	// 返回无符号整数b对应的IEEE 754格式二进制表示的8字节浮点数
	// uint64转float64
	math.Float64frombits(u64)

	// 如果x是一个负数或者负零，返回真
	// 判断是否小于0
	math.Signbit(-1)

	// 返回x的绝对值与y的正负号的浮点数
	math.Copysign(-12.34, -22.54) // -12.34

	// 返回不小于x的最小整数（的浮点值）
	// 计算数字只入不舍
	math.Ceil(12.34)

	// 返回不大于x的最小整数（的浮点值）
	// 计算数字只舍不入
	math.Floor(12.34)

	// 四舍五入
	math.Round(12.34)

	// 返回x的整数部分（的浮点值）
	math.Trunc(12.34)

	// 返回f的整数部分和小数部分，结果的正负号都相同
	math.Modf(12.34)

	// 参数x到参数y的方向上，下一个可表示的数值；如果x==y将返回x
	math.Nextafter(12.34, 15.67)

	// 同Nextafter类似
	math.Nextafter32(12.34, 15.67)

	// 返回x的绝对值
	math.Abs(-12.34) // 12.34

	// 返回x和y中最大值
	math.Max(12.3, 22)

	// 返回x和y中最小值
	math.Min(12.3, 22)

	// 返回x-y和0中的最大值
	math.Dim(12.3, 12)

	// 取余运算，可以理解为 x-Trunc(x/y)*y，结果的正负号和x相同
	math.Mod(22, 3)

	// IEEE 754差数求值，即x减去最接近x/y的整数值（如果有两个整数与x/y距离相同，则取其中的偶数）与y的乘积
	math.Remainder(12, 34)

	// 返回x的二次方根
	math.Sqrt(144) // 12

	// 返回x的三次方根
	math.Cbrt(27)

	// 返回Sqrt(p*p + q*q)，注意要避免不必要的溢出或下溢
	math.Hypot(2, 3)

	// 求正弦
	math.Sin(3)

	// 求余弦
	math.Cos(4)

	// 求正切
	math.Tan(5)

	// 函数返回Sin(x), Cos(x)
	math.Sincos(3)

	// 求反正弦（x是弧度）
	// Asin(±0) = ±0
	// Asin(x) = NaN if x < -1 or x > 1
	math.Asin(0.5)

	// 求反余弦（x是弧度）
	// Acos(x) = NaN if x < -1 or x > 1
	math.Acos(0.5)

	// 求反正切（x是弧度）
	// Atan(±0) = ±0
	// Atan(±Inf) = ±Pi/2
	math.Atan(0.5)

	// 类似Atan(y/x)，但会根据x，y的正负号确定象限
	math.Atan2(0.5, 0.2)

	// 求双曲正弦
	math.Sinh(2)

	// 求双曲余弦
	math.Cosh(2)

	// 求双曲正切
	math.Tanh(2)

	// 求反双曲正弦
	math.Asinh(2)

	// 求反双曲余弦
	math.Acosh(2)

	// 求反双曲正切
	math.Atanh(2)

	// 求自然对数
	math.Log(2)

	// 等价于Log(1+x)。但是在x接近0时，本函数更加精确
	math.Log(0.0001)

	// 求2为底的对数；特例和Log相同
	math.Log2(2)

	// 求10为底的对数；特例和Log相同
	math.Log10(10)

	// 返回x的二进制指数值，可以理解为Trunc(Log2(x))
	math.Logb(10)

	// 类似Logb，但返回值是整型
	math.Ilogb(12.34)

	// 返回一个标准化小数frac和2的整型指数exp，满足f == frac * 2**exp，且0.5 <= Abs(frac) < 1
	frac, exp := math.Frexp(12.34)

	// Frexp的反函数，返回 frac * 2**exp
	math.Ldexp(frac, exp)

	// 返回E**x；x绝对值很大时可能会溢出为0或者+Inf，x绝对值很小时可能会下溢为1
	math.Exp(12)

	// 等价于Exp(x)-1，但是在x接近零时更精确；x绝对值很大时可能会溢出为-1或+Inf
	math.Expm1(12)

	// 返回2**x
	math.Exp2(12)

	// 返回x**y
	math.Pow(3, 4)

	// 返回10**e
	math.Pow10(2)

	// 伽玛函数（当x为正整数时，值为(x-1)!）
	math.Gamma(10)

	// 返回Gamma(x)的自然对数和正负号
	math.Lgamma(10)

	// 误差函数
	math.Erf(10.01)

	// 余补误差函数
	math.Erfc(10.01)

	// 第一类贝塞尔函数，0阶
	math.J0(2)

	// 第一类贝塞尔函数，1阶
	math.J1(2)

	// 第一类贝塞尔函数，n阶
	math.Jn(2, 2)

	// 第二类贝塞尔函数，0阶
	math.Y0(2)

	// 第二类贝塞尔函数，1阶
	math.Y1(2)

	// 第二类贝塞尔函数，n阶
	math.Yn(2, 2)
}
```

## math/big

```go
package main

import "math/big"

// big包实现了大数字的多精度计算
func main() {

	exampleInt()
}

func exampleInt() {

	// 创建一个值为x的*big.Int
	i := big.NewInt(10)

	// 返回x的int64表示，如果不能用int64表示，结果为undefined
	i.Int64()

	// 返回x的uint64表示，如果不能用uint64表示，结果为undefined
	i.Uint64()

	// 返回x的绝对值的大端在前的字节切片表示
	i.Bytes()

	// 返回字符串
	i.String()

	// 返回x的绝对值的字位数，0的字位数为0
	i.BitLen()

	// 提供了对x的数据不检查而快速的访问，返回构成x的绝对值的小端在前的word切片
	// 该切片与x的底层是同一个数组，本函数用于支持在包外实现缺少的低水平功能，否则不应被使用
	i.Bits()

	// 返回第i个字位的值，即返回(x>>i)&1。i必须不小于0
	i.Bit(1)

	// 将z设为x并返回z
	i.SetInt64(11)

	// 将z设为x并返回z
	i.SetUint64(11)

	// 将buf视为一个大端在前的无符号整数，将z设为该值，并返回z
	i.SetBytes([]byte("11"))

	// 将z设为s代表的值（base为基数）
	// 返回z并用一个bool来表明成功与否。如果失败，z的值是不确定的，但返回值为nil
	// 基数必须是0或者2到MaxBase之间的整数。如果基数为0，字符串的前缀决定实际的转换基数："0x"、"0X"表示十六进制；"0b"、"0B"表示二进制；"0"表示八进制；否则为十进制
	i.SetString("11", big.MaxBase)
}
```


## math/rand

```go
package main

import (
	"fmt"
	"math/rand"
	"os"
	"text/tabwriter"
	"time"
)

// rand包实现了伪随机数生成器
func main() {

	// 全局随机数
	example()

	// 自定随机数
	exampleRand()

	// 切片值随机排序
	exampleShuffle()
}

func example() {

	// 使用给定的seed来初始化生成器到一个确定的状态
	// 如未调用，默认资源的行为就好像调用了Seed(1)
	// 如果需要实现随机数，seed值建议为 时间戳
	// 如果seed给定固定值，结果永远相同
	rand.Seed(time.Now().UnixNano())

	// 返回一个非负的伪随机int值
	fmt.Println(rand.Int())

	// 返回一个取值范围在[0,n]的伪随机int值，如果n<=0会panic
	fmt.Println(rand.Intn(100))

	// 返回一个int32类型的非负的31位伪随机数
	fmt.Println(rand.Int31())

	// 返回一个取值范围在[0,n]的伪随机int32值，如果n<=0会panic
	fmt.Println(rand.Int31n(100))

	// 返回一个int64类型的非负的63位伪随机数
	fmt.Println(rand.Int63())

	// 返回一个取值范围在[0, n]的伪随机int64值，如果n<=0会panic
	fmt.Println(rand.Int63n(100))

	// 返回一个uint32类型的非负的32位伪随机数
	fmt.Println(rand.Uint32())

	// 返回一个uint64类型的非负的64位伪随机数
	fmt.Println(rand.Uint64())

	// 返回一个取值范围在[0, 1]的伪随机float32值
	fmt.Println(rand.Float32())

	// 返回一个取值范围在[0, 1]的伪随机float64值
	fmt.Println(rand.Float64())

	// 返回一个服从标准正态分布（标准差=1，期望=0）、取值范围在[-math.MaxFloat64, +math.MaxFloat64]的float64值
	fmt.Println(rand.NormFloat64())

	// 返回一个服从标准指数分布（率参数=1，率参数是期望的倒数）、取值范围在(0, +math.MaxFloat64]的float64值
	fmt.Println(rand.ExpFloat64())

	// 返回一个有n个元素的，[0,n)范围内整数的伪随机排列的切片
	fmt.Println(rand.Perm(15))
}

func exampleRand() {

	// 初始化一个Source，代表一个生成均匀分布在范围[0, 1<<63)的int64值的（伪随机的）资源
	// 需要随机，建议使用时间戳，详情查看example()
	source := rand.NewSource(99)

	// 初始化*rand.Rand
	r := rand.New(source)

	// 初始化一个过滤器
	w := tabwriter.NewWriter(os.Stdout, 1, 1, 1, ' ', 0)
	defer w.Flush()

	show := func(name string, v1, v2, v3 interface{}) {
		fmt.Fprintf(w, "%s\t%v\t%v\t%v\n", name, v1, v2, v3)
	}

	show("Float32", r.Float32(), r.Float32(), r.Float32())
	show("Float64", r.Float64(), r.Float64(), r.Float64())
	show("ExpFloat64", r.ExpFloat64(), r.ExpFloat64(), r.ExpFloat64())
	show("NormFloat64", r.NormFloat64(), r.NormFloat64(), r.NormFloat64())
	show("Int31", r.Int31(), r.Int31(), r.Int31())
	show("Int63", r.Int63(), r.Int63(), r.Int63())
	show("Uint32", r.Uint32(), r.Uint32(), r.Uint32())
	show("Intn(10)", r.Intn(10), r.Intn(10), r.Intn(10))
	show("Int31n(10)", r.Int31n(10), r.Int31n(10), r.Int31n(10))
	show("Int63n(10)", r.Int63n(10), r.Int63n(10), r.Int63n(10))
	show("Perm", r.Perm(5), r.Perm(5), r.Perm(5))
}

func exampleShuffle() {

	numbers := []byte("12345")
	letters := []byte("ABCDE")

	// 随机交换数切片的位置, n应为切片的长度，n < 0 会panic
	rand.Shuffle(len(numbers), func(i, j int) {
		numbers[i], numbers[j] = numbers[j], numbers[i]
		letters[i], letters[j] = letters[j], letters[i]
	})
	for i := range numbers {
		fmt.Printf("%c: %c\n", letters[i], numbers[i])
	}
}
```