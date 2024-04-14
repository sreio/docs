> Cobra 是一个用于创建功能强大的现代 CLI 应用程序的库。
>
> 许多 Go 项目都使用了 Cobra，例如 Kubernetes、Hugo 和 GitHub CLI 等等。该列表包含使用 Cobra 的更多项目。

- 基于子命令的简易 CLI： app server , app fetch 等。
- 完全符合 POSIX 标准的标志（包括长短版本）
- 嵌套子命令
- 全局、本地和层叠旗帜
- 聪明的建议（ app srver ......你是说 app server 吗？）
- 为命令和标记自动生成帮助
- 分组子命令帮助
- 自动识别 -h 、 --help 等帮助标记。
- 为您的应用程序（bash、zsh、fish、powershell）自动生成 shell 自动完成功能
- 为您的应用程序自动生成 man 页
- 命令别名使您可以在不破坏命令的情况下进行更改
- 可灵活定义自己的帮助、用法等。
- 可选择与 viper 无缝集成，用于 12 要素应用程序

- GitHub仓库地址: https://github.com/spf13/cobra