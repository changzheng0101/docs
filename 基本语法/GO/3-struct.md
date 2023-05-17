# struct

## 定义

一组属性结合在一起。类似于JavaScript中的对象

## 使用

拥有lastName和firstName的person将会这样定义：

```go
type person struct {
    firstName string
    lastName string
}

func main(){
    // alice := person{"alice","andnsom"}
    
    // var alice person
    // person.firstName = "alice" person.lastName="andnsom"
    
    alice := person{firstName: "alice", lastName: "andnsom"}
    fmt.Println(alice)
}
```

## 默认值

| type   | 默认值 |
| ------ | ------ |
| string | ""     |
| int    | 0      |
| float  | 0      |
| bool   | false  |

## 结构体嵌套

一个结构体中的某个属性可以是另一个结构体

```go
package main

import (
	"fmt"
)

type addressInfo struct {
	email string
	zip   int
}

type person struct {
	firstName string
	lastName  string
	addressInfo
}

func main() {
	jim := person{
		firstName: "jim",
		lastName:  "adason",
		addressInfo: addressInfo{
			email: "test@qq.com",
			zip:   123456,
		},
	}
	jim.updateName("newName")
	jim.print()
    // output: {firstName:jim lastName:adason addressInfo:{email:test@qq.com zip:123456}}
}

// 这将不会改变p中的值
func (p person) updateName(newFirstName string) {
	p.firstName = newFirstName
}

func (p person) print() {
	fmt.Printf("%+v", p)
}
```

