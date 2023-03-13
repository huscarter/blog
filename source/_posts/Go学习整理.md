---
title: Go学习整理
date: 2022-09-21 20:29:44
categories: 计算机语言
tags: 
    - Go
description: 最近看到技术群里有人要求用 Go 语言开发项目，之前一直对 Go 语言比较感兴趣，所以这几天打算把 Go 学习一下。
---

## 简介
Go（又称 Golang）是 Google 的 Robert Griesemer，Rob Pike 及 Ken Thompson 开发的一种静态强类型、编译型语言。Go 语言语法与 C 相近，但功能上有：内存安全，GC（垃圾回收），结构形态及 CSP-style 的并发计算。

## 开发环境（以mac为例）
### 安装 Go 包
1. 安装包下载：[国外下载地址](https://golang.org/dl/)、[国内下载地址](https://golang.google.cn/dl/)
2. 安装到推荐目录:/usr/local
- mac 的 pkg 默认会安装到/usr/local目录
- linux 需要自己指定
```
tar -C /usr/local -xzf go1.19.1.darwin-amd64.pkg
```
3. 环境配置
```
vim ~/.bash_profile
export PATH=$PATH:/usr/local/go/bin
```
4. 可通过下面的命令校验 Go 是否安装成功
```
// 查看 go 的版本号
go version
// 查看 go 的环境
go env
```

### 安装ide（以vscode为例）
1. 下载安装vscode：[地址](https://code.visualstudio.com/)
2. 安装vscode的插件
- vscode 中 通过 extensions 安装 go 插件
- 通过下面的命令安装其余插件
```
go env -w GOSUMDB=off
go env -w GOPROXY=https://goproxy.cn
 
go install -v github.com/rogpeppe/godef@latest
go install -v github.com/golang/lint/golint@latest
go install -v github.com/lukehoban/go-find-references@latest
go install -v github.com/lukehoban/go-outline@latest
go install -v sourcegraph.com/sqs/goreturns@latest
go install -v golang.org/x/tools/cmd/gorename@latest
go install -v github.com/tpng/gopkgs@latest
go install -v github.com/newhook/go-symbols@latest
go install -v golang.org/x/tools/cmd/guru@latest
go install -v github.com/derekparker/delve/cmd/dlv@latest
```

### 运行HelloWorld
新建一个Test.go文件，写入下面的代码，然后点击 run code 按钮则会在控制台打印出"HelloWorld"字符串。
```
package main

import "fmt"

func main() {
	fmt.Println("Hello world")
}
```

## Go 语言知识
### 基础语法
1. 行分隔符
Go 语言换行当作一个语句的结束，如果想在一行写多条语句，需要用“;”做分隔符。
2. 标识符
英文字母和下划线可以作为标识符的开头，数字不行。小写字母开头的变量是私有的（protected），大写字母开头的变量是公共的（public）
3. 注释
```
// 单行注释，官方代码一般使用这种单行注释来写多行注释。

/*
 多行注释
 用的比较少
 */
```

### 数据类型
下面是平时会用到的数据类型。对于整型我们平时用int就行，但是在二进制传输、读写文件的结构描述时，为了保持文件的结构不会受到不同编译目标平台字节长度的影响，推荐使用指定长度的整型。

| 类型     | 关键字      | 长度   | 说明(t代码关键字)                              |
| -------- | -----------| ------ | ------------------------------------------ |
| 布尔型   | bool        |        |                                            |
| 整型     | int         |        | int8、int16、int32、int64                   |
| 无符号整型| uint        |        | uint8、uint16、uint32、uint64               |
| 浮点型   | float32     |        | float32、float64                            |
| 字符串   | string      |        |                                            |
| 数组    |  []type     |         | []int、[]string                            |
| 切片    |  []type     |         | 和数组一样定义，就是多了容量控制方法             |
| 指针    |  *type      |         | *int、*string                              |
| 通道    |  chan       |         | chan int、chan string；用于发送和接受数据      |
| map    |  map        |         |                                             |
| 结构化  |  struct     |         | 类似swift的结构体                             |

- Go 语言数据转换都是需要显示转换的，转换代码如下
```
var i int = 10
var f float32 = float32(i)
```

### 变量
定义在函数里的变量是局部变量，定义在函数外的变量是全局变量，通过 var 关键字声明。
```
var v1 string = "Hello" // 变量普通的定义形式
v2 := "world" // 变量省略指定类型的定义形式
var v3 [3]int = [3]int{1,2,3} // 数组定义的完整形式
var v4 = [3]int{1,2,3}
v5 := [...]int{1,2,3} 
```

### 函数
Go 语言最少有个 main() 函数。函数接收参数并可返回结果，可以返回多个结果哦。
```
// 普通带有返回数据的函数定义
func test(v1 int, v2 string) (int, string) {
	return 0, "str"
}

// 递归函数定义
func factorial(n uint64) uint64 {
	if n > 0 {
		result := n * factorial(n-1)
		return result
	}
	return 1
}

// 递归函数调用
var result uint64 = factorial(uint64(15))
```

### 条件语句
1. if else
```
func test () {
	var a int = 10
	if a < 0 {
		fmt.Print("小于0")
	} else {
		fmt.Print("大于或等于0")
	}
}
```
2. switch
switch 语句 case 后面不需要写break，如果有需求在 case 之后还需要继续执行，可以通过添加 fallthrough 来实现。
```
func test() int {
	var a int = 10
	var result int
	switch a {
		case 0:
			result = 0
		case 1:
			result = 1
			fallthrough
		default:
			result = -1
	}
	return result
}
```
3. select
select 的用法和 switch 很类似，switch 是 执行满足 case 的语句，select 是公平随机执行满足 case 的语句，如果当前没有满足的 case 则进行等待。
注意：select 的 case 语句必须是通道的信号操作
```
type Node struct {
	connected chan int
}

func test(n Node) {
	select {
	case <-n.connected:
		log.Println("接收到连接包")
	case <-time.After(time.Second * 5):
		log.Println("接收连接包超时")
	}
}
```

### 循环语句
1. for
```
func test () {
	var a int = 10
	for i := 0; i < a; i++ {
		fmt.Println(i)
	}
}
```

### 数组
1. 数组定义
```
var arr = []int{1, 2, 3, 4, 5} // 自动识别长度
// var arr = [5]int{1, 2, 3, 4, 5}  // 确定长度
// var arr = [...]int{1, 2, 3, 4, 5} // 不确定长度
```
2. 数组访问
```
var arr = []int{0, 2, 3, 4, 5}
arr[0] = 1 // 数组赋值
a0 := arr[0] // 通过下标取值
alen := len(arr) // 获取数组的长度：5
acap := cap(arr) // 获取数组的容量（数组的容量是固定的=长度）：5
// 数组的迭代
for i, v := range arr {
  fmt.Println(i, v)
}
```
3. 多维数组
```
var arr = [][]int{[1,2,3,4,5],[6,7,8,9,10]}
var arr = [][]int{{1, 2, 3, 4, 5}, {6, 7, 8, 9, 10}}
fmt.Println(len(arr), cap(arr)) // 2, 2
fmt.Println(arr[1][0]) // 6 
```

### 切片
切片实就是可变容量的数组。
1. 切片定义
```
var slice = []int{1, 2, 3, 4, 5} // 切片不需要指定长度
// var slice = make ([]int, 3, 30)  // 指定切片的长度为3，容量为30
```
2. 切片访问
```
var slice = []int{0, 2, 3, 4, 5}
fmt.Println(slice[0])
fmt.Println(slice[0:4]) // 打印子切片从索引0(包含) 到索引4(不包含)
fmt.Println(slice[:4]) // 同slice[0:4]
fmt.Println(slice[0:]) // 同slice[0:4]
```
3. 切片操作（append、copy）
- append追加元素
```
var slice = []int{0, 1, 2, 3}
slice = append(slice, 4)
fmt.Println(slice) // [0,1,2,4]
```
- copy复制切片
想增加切片的容量，我们必须创建一个新的更大的切片并把原分片的内容都拷贝过来。
```
var slice = []int{0, 1, 2, 3}
var slice1 = make([]int, len(slice), cap(slice)*2)
copy(slice1, slice)
```

### map
map 一种无序键值对集合，同 java 的 Map， swift 的 dic。
1. map 定义
```
// var m map[string] // 只定义不初始化
var m = map[string]int{"1": 1, "2": 2} // 定义和初始化
fmt.Print(m) // [1:1, 2:2]
```
2. map 操作
```
var m = map[string]int{"1": 1, "2": 2}
m["3"] = 3 // map 添加新值
delete(m, "2") // map删除元素
fmt.Println(m) // [1:1, 3:3]
```
3. map 迭代
```
var m = map[string]int{"1": 1, "2": 2}
for k, v := range m {
  fmt.Println(k, v)
}
```

### 指针
指针：指向一个值的内存地址。
```
var i int = 10 // 定义一个值
var ip *int = &i // 定义和初始化一个指针
fmt.Print(*ip) // 读取指针指向内存的值 10
```

### 结构体
Go 语言中数组可以存储同一类型的数据，但在结构体中我们可以为不同项定义不同的数据类型。
1. 结构体的定义和访问
```
type Book struct {
	id     int
	title  string
	author string
}

func test() {
	var book = Book{1, "练习册", "weihh"}
	fmt.Println(book.title) // 练习册
}
```

### 接口
Go 语言它把所有的具有共性的方法定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口。
```
type Phone interface {
	call()
}

type NokiaPhone struct {
}

func (nokiaPhone NokiaPhone) call() {
	fmt.Println("I am Nokia, I can call you!")
}

type IPhone struct {
}

func (iPhone IPhone) call() {
	fmt.Println("I am iPhone, I can call you!")
}
```

### 并发
Go 语言支持并发，我们只需要通过 go 关键字来开启 goroutine 即可。goroutine 是轻量级线程，goroutine 的调度由 Golang 运行时进行管理。
```
func main() {
	go test("Hello") // 通过 go 开启 goroutine
}

func test(s string) {
	// to do something
}
```

### 通道
1. 通道的定义和基本使用
通道（chan）是用来传递数据的一个数据结构。可用于两个 goroutine 之间通过传递一个指定类型的值来同步运行和通讯。操作符 <- 用于指定通道的方向，发送或接收。
```
func main() {
	var arr = []int{1, 2, 3, 4, 5}
	var ch = make(chan int)
	go sum(arr, ch)
	fmt.Println(<-ch)
}

func sum(s []int, ch chan int) {
	var total int = 0
	for _, v := range s {
		total += v
	}
	ch <- total // 把 total 发送到通道 ch
}
```
2. 通道的缓冲区
通过 make 的第二个参数指定缓冲区大小。
```
var ch = make(chan int, 10)
```
- 没有缓冲区的通道，发送方会阻塞直到接收端接收数据。
- 带有缓冲区的通道，发送和接收都是处于异步状态，发送的数据会放到缓冲区等待接收端自主获取。
- 如果缓冲区满了，发送方会阻塞直到接收方接收了数据，导致缓冲区有了新的空间；接收方在缓冲区有数据前会一直阻塞。
3. 关闭通道
```
var chan ch = make(chan int)
close(ch) // 关闭通道
```
4. 通道遍历
```
func main() {
	var arr = []int{1, 2, 3, 4, 5}
	var ch = make(chan int)
	go short(arr, ch)
	for v := range ch { // 因为 short 方法调用了 close 关闭了通过，否则 for 在接收完数据会进入阻塞状态。
    fmt.Println(v)
  }
}

func short(s []int, ch chan int) {
	for _, v := range s {
		ch <- v
	}
	close(ch)
}
```

### 错误处理
Go 语言通过内置的错误接口提供了非常简单的错误处理机制，Go 的错误接口源码如下：
```
type error interface {
    Error() string
}

// 使用错误接口的例子
errors.New("错误信息")
```



## 链接
1. [runboom Go 学习](https://www.runoob.com/go/go-basic-syntax.html)