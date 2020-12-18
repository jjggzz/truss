# 介绍
基于metaverse/truss修改,truss是go-kit的一款代码生成脚手架,可以快速生成go-kit的endpoint和transport层代码,免去开发人员编写encode和decode方法工作,极大的提升了工作效率.目前只transport层只支持grpc和http协议(如果你的项目使用的是thrift,它可能有点不和适)

# 安装方式
使用gomod方式构建
```
// 关闭gomod
export GO111MODULE=off
// 下载源码到gopath的src目录下
go get github.com/jjggzz/truss
// 进入项目目录
cd $GOPATH/src/github.com/jjggzz/truss
// 开启gomod
export GO111MODULE=on
// 下载依赖 (windows直接执行wininstall.bat即可)
make dependencies
// 构建(若提示running "go-bindata": exec: "go-bindata": executable file not found in $PATH,请将$GOPATH/bin配置到环境变量)
make
```
# 使用方式
编辑proto文件 test.proto
```
syntax = "proto3";

package proto;

service Test {
  rpc Show (Empty) returns (Empty) {}

}

message Empty {

}
```
执行命令
```
// --svcout ./  生成项目在当前文件夹下
// --pbpkg proto pb文件放在项目的proto文件夹下
truss test.proto --svcout ./  --pbpkg proto
```
# 生成结构
```
.
├── test-service
|   ├── cmd 
|   │   └── test
|   │       └── main.go
|   ├── proto (通过pbpkg参数生成,若不指定,pb文件生成在test-service下)
|   |   └── echo.pb.go
|   ├── handlers (重复执行生成命令不会修改此文件夹下文件的内容)
|   │   ├── handlers.go (在这里调用自己写的业务逻辑)
|   │   ├── hooks.go (启动时的钩子函数,监听停止)
|   │   └── middlewares.go (服务运行的中间件,可以在这里为端点集成熔断\限流\链路追踪\调用统计等等中间件,go-kit提供了相应的解决方案)
|   └── svc (重复执行生成命令会重新生成此文件夹下的内容.若此文件夹下有自己创建的文件,那些文件不会被修改)
|       └── ...
└── test.proto
```
# 更详细的使用方法
https://github.com/metaverse/truss
# 未来的工作
支持生成负载均衡的调用客户端(现在的客户端代码只支持单点调用)\
支持生成服务注册代码(现在需要手动编写)
