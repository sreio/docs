> jwt JSON Web令牌（JWT）的Golang实现。

- GitHub仓库地址： https://github.com/golang-jwt/jwt



## 示例

```go
package main

import (
    "errors"
    "fmt"
    "time"

    "github.com/golang-jwt/jwt/v4"
)

type MyClaims struct {
    Username string `json:"username"`
    jwt.RegisteredClaims
}

const TokenExpireDuration = time.Second * 60

var MySecret = []byte("我是加密的key")

func main() {
    fmt.Println(time.Now())
    token, err := GenToken("weidada")
    if err != nil {
        fmt.Println(err)
        return
    }
    fmt.Println(token)

    mc, err := ParseToken(token)
    if err != nil {
        fmt.Println(err)
        return
    }
    fmt.Println(mc)
}

// GenToken 生成JWT
func GenToken(username string) (string, error) {
    // 创建一个我们自己的声明
    c := MyClaims{
        username, // 自定义字段
        jwt.RegisteredClaims{
            ExpiresAt: jwt.NewNumericDate(time.Now().Add(TokenExpireDuration)),
            Issuer:    "weidada-api", // 签发人
        },
    }

    // 使用指定的签名方法创建签名对象
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, c)
    // 使用指定的secret签名并获得完整的编码后的字符串token
    return token.SignedString(MySecret)
}

// ParseToken 解析JWT
func ParseToken(tokenString string) (*MyClaims, error) {
    // 解析token
    token, err := jwt.ParseWithClaims(tokenString, &MyClaims{}, func(token *jwt.Token) (i interface{}, err error) {
        return MySecret, nil
    })

    if err != nil {
        return nil, err
    }
    if claims, ok := token.Claims.(*MyClaims); ok && token.Valid { // 校验token
        return claims, nil
    }
    return nil, errors.New("invalid token")
}
```