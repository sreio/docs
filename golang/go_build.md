### windows.exe:

```bash
#64bit
GOOS=windows GOARCH=amd64 go build -o bin/app-amd64.exe app.go#32-bit
GOOS=windows GOARCH=386 go build -o bin/app-386.exe app.go
```

### Linux:

```bash
# 64-bit
GOOS=linux GOARCH=amd64 go build -o bin/app-amd64-linux app.go# 32-bit
GOOS=linux GOARCH=386 go build -o bin/app-386-linux app.go
```

### MACOS:

```bash
# 64-bit
GOOS=darwin GOARCH=amd64 go build -o bin/app-amd64-darwin app.go# 32-bit
GOOS=darwin GOARCH=386 go build -o bin/app-386-darwin app.go
```

如果项目中存在静态资源，可以使用`go-bindata`可以参考https://toutiao.io/posts/yqh721/preview

```bash
go-bindata -o=xxx -pkg=xxx xxx/...-o # 指定打包后生成的go文件路径
-pkg # 指定go文件的包名
config/... # 指定需要打包的静态文件路径
```