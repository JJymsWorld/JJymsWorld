---
title: GoModules使用
date: 2022-07-16 16:48:32
tags: 
 - [Golang]
categories:
 - [gallery]
---

## GoModule相关命令

go mod init 生成go.mod文件

go mod download 下在go.mod文件中指明的所有依赖

go mod tidy 整理现有的依赖

go mod graph 查看现有的依赖结构

go mod edit 编辑go.mod文件

go mod vendor 导出项目所有的依赖到vendorm目录

go mod verify 校验一个模块是是否被篡改过

go mod why 查看为什么需要依赖模块



## GoModu环境变量

GO111MODULE = on

GOPROXY = https://goproxy.cn,direct     (七牛云仓库)

GOSUMDB （设置GOPROXY之后无需设置自动覆盖）

GONOPROXY，GONOSUMDB，GOPRIVATE（使用私人仓库时使用，将不会通过GOPROXY进行校验）

如 go env -w GOPRIVATE="git.example.com" 表示git.example.com是私有仓库，不会进行GOPROXY下载和校验

还可设置通配符进行匹配如"*.example.com"



## Go env 环境变量

go env 查看环境变量

通过go env -w GOPROX=------------------------------来设置go env中的变量

或者通过Linux export (编辑.bashrc或者.zshrc等)



## Go modules 初始化项目

1. 开启Go Module模块  即保证GO111MODULE=on
2. 任意文件夹创建项目(不需要在GOPATH下)
3. 创建go.mod文件 使用go mod init + 模块名称
4. 在该项目中创建源代码，编写源代码，若代码中依赖某个库，如github.com/aceld/zin/znet
5. 执行go get github.com/aceld/zinx/znet来下载模块到本地，存储路径为GOPATH下的pkg\mod中

在执行go get下载依赖之后会在go.mod文件中新增一行`require github.com/aceld/zinx v1.0.1 // indirect `其含义是当前模块依赖为github.com/aceld/zinx，依赖的版本是v1.0.1，indirect表示间接依赖（因为项目直接依赖的是znet包，所以间接依赖是zinx包）



还会生成一个go.sum文件，内容形式为

```go
github.com/aceld/zinx v1.0.1 h1:WnahGyE7tDJvkJRVK2VI/m57aHEeUjr12EAYpOYW3ng=
github.com/aceld/zinx v1.0.1/go.mod h1:Tth0Fmjjpel0G8YjCz0jHdL0sXlc4p3Km/l/srvqqKo=
github.com/golang/protobuf v1.3.3/go.mod h1:vzj43D7+SQXF/4pzW/hwtAqwc6iTitCiVSaWz5lYuqw=

```

go.sum 文件的作用：罗列当前项目直接或者间接依赖的所有模块版本，保证今后项目依赖的版本不会被篡改

h1:hash 表示整体项目的zip文件打开之后的全部文件的校验和来生成的hash，如果这项不存在，可能表示依赖的库可能用不上

xxx/go.mod h1:hash      go.mod文件生成的hash



## 修改模块依赖关系

使用go mod edit -replace=版本名称1=版本名称2来实现版本替换
