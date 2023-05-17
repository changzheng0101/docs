# HelloWorld

## 源代码

```go
package main

import "fmt"

func main(){
    fmt.Println("hello world")
}
```

## 执行代码

```bash
go run main.go
---------------
go build main.go
./main.exe
```

## go的常见命令

|    命令    |                 解释                 |
| :--------: | :----------------------------------: |
|  go build  |       **编译**一个或者多个文件       |
|   go run   |    **编译并执行一个**或者多个文件    |
|   go fmt   |              格式化代码              |
| go install |            安装go的依赖包            |
|  go  get   | 也是安装依赖包，但是这个会下载源代码 |
|  go test   |           运行go的测试用例           |

## package

在代码的第一行声明了`package main`，一个package可以理解为一个项目，在同一个package下的所有代码共同构成了一个项目；一共可以形成两种项目，第一种是可执行的，也就是直接生成exe文件，另一种生成依赖文件，类似于开发库供别人使用，区别这两者的主要方式是**只有package main生成可执行文件，其余的package都是依赖，并且生成可执行文件时里面一般需要有main函数。**如果是依赖，运行`go build xxx.go`命令将不会输出任何文件。

## import

导入其他包进行使用

## func

```go
func 函数名(参数列表){
    // 函数体
}
```



