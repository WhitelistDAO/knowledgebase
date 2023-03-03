##go zero 实现 rpc

###Goctl Rpc是goctl脚手架下的一个rpc服务代码生成模块，支持proto模板生成和rpc服务代码生成，通过此工具生成代码你只需要关注业务逻辑编写而不用去编写一些重复性的代码。这使得我们把精力重心放在业务上，从而加快了开发效率且降低了代码出错率。

###特性

* 简单易用
* 快速提升开发效率
* 出错率低
* 贴近protoc

###实现

####方式1
通过命令 goctl rpc new ${servieName}生成

如生成greet rpc服务：

	$ goctl rpc new greet

执行后代码结构如下:

	cd greet && tree
	.
	├── etc
	│   └── greet.yaml
	├── go.mod
	├── go.sum
	├── greet
	│   ├── greet.go
	│   ├── greet.pb.go
	│   └── greet_grpc.pb.go
	├── greet.go
	├── greet.proto
	└── internal
	    ├── config
	    │   └── config.go
	    ├── logic
	    │   └── pinglogic.go
	    ├── server
	    │   └── greetserver.go
	    └── svc
	        └── servicecontext.go

####方式2

* 生成proto模板

	$ goctl rpc template -o=user.proto

	syntax = "proto3";
	
	package user;
	option go_package="./user";
	
	message Request {
	  string ping = 1;
	}
	
	message Response {
	  string pong = 1;
	}
	
	service User {
	  rpc Ping(Request) returns(Response);
	}

* 生成rpc服务代码

	$ goctl rpc protoc user.proto --go_out=. --go-grpc_out=. --zrpc_out=.

###goctl的rpc用法
	
	$ goctl rpc protoc -h
	NAME:
	   goctl rpc protoc - generate grpc code
	
	USAGE:
	   example: goctl rpc protoc xx.proto --go_out=./pb --go-grpc_out=./pb --zrpc_out=.
	
	DESCRIPTION:
	   for details, see https://go-zero.dev/cn/goctl-rpc.html
	
	OPTIONS:
	   --zrpc_out value  the zrpc output directory
	   --style value     the file naming format, see [https://github.com/zeromicro/go-zero/tree/master/tools/goctl/config/readme.md]
	   --home value      the goctl home path of the template
	   --remote value    the remote git repo of the template, --home and --remote cannot be set at the same time, if they are, --remote has higher priority
	                     The git repo directory must be consistent with the https://github.com/zeromicro/go-zero-template directory structure
	   --branch value    the branch of the remote repo, it does work with --remote
	   --verbose, -v     enable log output


可以理解为 zrpc 代码生成是用 goctl rpc $protoc_command --zrpc_out=${output} 模板，如原来生成 grpc 代码指令为

	$ protoc user.proto --go_out=. --go-grpc_out=.

则生成 zrpc 代码指令就为

	$ goctl rpc protoc user.proto --go_out=. --go-grpc_out=. --zrpc_out=.
