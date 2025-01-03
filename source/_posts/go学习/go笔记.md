---
layout: posts
title: "go笔记"
date: 2025-01-01 22:16:15
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "go笔记"
tags:
- "go"
---

简介 <!--more-->



#  安装

1、注意 环境变量中 GOPATH； GOROOT的配置。

2、还要 注意 是 模块模式还是 gopath模式。

3、用vscode编辑器时一定要记得在编辑器中 指定gopath的路径，不然会找不到。

# 杂鱼笔记

1、go 一个方法内重复定义 变量非法；

2、go 没有隐式类型转换，变量声明和赋值要一致；

3、go 声明变量 有个默认值；

4、go有类型推断；

5、 用vscode时，注意 go 代码规范是 冗余非法，一定要先在代码块中用到了 某个 库，再写 import，不然直接保存会导致 编辑器 把 冗余import 库直接当写错了删除。

6、uint8 这样的无符号 整型 范围是 2^7 + 2^7-1 = 0~255 , 整数部分是 int8 -128~127的两倍。

7、go 用反引号 `` 输出带特殊符号的原始字符串.

8、go 的 string 转其它类型一定要保证 能够转，不然会按照 目标类型的默认值来 赋值；

# 指针

0、指针概念；

```go 
package main

import "fmt"

func main() {
	var age int = 18
	fmt.Println(&age) //变量内存地址

	var ptr *int = &age // 可以理解为 一个 指向 int类型的指针类型，里面放的是 指向 int类型 的地址；
	fmt.Println("ptr存储空间的地址为：", &ptr) //获取指针本身的内存地址；
	fmt.Println("ptr指向的数值为：", *ptr) //获取指针指向的值。
}
```

1、可以通过指针来改变指向值；

```go
package main

import "fmt"

func main() {
	var age int = 18
	var ptr *int = &age
	*ptr = 20
	fmt.Println(age)
}
```

2、指针变量接收的一定是地址值；

3、指针变量的定义一定要和实际值匹配，例如 *float 类型的指针 就不能和 &num num 是一个 int8 类型的变量地址匹配；

# 标识符

'_' 只能被当作忽略的标识符，不能单独当作标识符使用。

1、main包 是一个程序的入口包，main函数所在的包，建议定义为main包；

2、**变量名、函数名、常量 首字母大写，则可以被其它包访问，首字母小写则只能本包中使用；**

```go
//1、go 默认的模式不是 模块模式，模块模式能 跳出gopath 的影响，但是默认用gopath模式；
export GO111MODULE=off 
//2、gopath模式中，包都是从$GOPATH/src 路径后开始计算的，import 后面写相对路径即可；
package main

import (
	"fmt"
	"goproject/demo_package/test"
)

func main() {
	fmt.Println(test.StuNo)
}
//3、用vscode编辑器时一定要记得在编辑器中 指定gopath的路径，不然会找不到；
```

3、go语言获取控制台输入的 时候，scanln、scanf 传入的是待修改 值的地址 如 `&age`. 

# 分支结构

​		go 的 switch case 中 加上 fallthrough 会带来 一层的穿透。

​		goto 到某个标签的 语句最好少用，因为可能导致 逻辑混乱。



# 函数

go不支持 函数重载（同一函数 不同形参）

利用指针，实现一个函数内改变函数外的值

```go
package main

import "fmt"

var str string = "abcdefg"

// 模拟在一个函数中改变外面的值，利用指针。
func fun(args ...*int) {
	for i := range args {
		*args[i] = 30
		fmt.Print("fun=====", *args[i])
	}
}

func main() {
	var age int = 10
	fun(&age)
	fmt.Print("main----- ", age)
}
```

函数直接当形参

```go
package main

import "fmt"

func fun(num int) {
	fmt.Println(num)
}
// 把函数当形参
func test(num1 int, num2 float32, testFunc func(int)) {
	fmt.Println("---test")
}
func main() {
	// var age int = 10
	// fun(&age)
	a := fun
	// fmt.Printf("main----- %T,%T\n", a, fun)
	// a(10)
	test(1, 3.14, a)
}
```

go支持自定义类型

```go
type myFunc func(int)

func test01(num1 int, num2 float64, testFunc myFunc) {
	fmt.Println("---test01")
}
```

go支持对函数返回值命名

```go
func test02(num1 int, num2 int) (sum int, sub int) {
	sum = num1 + num2 //sum := num1 + num2由于这两个变量已经在函数签名中声明，你不能再使用短变量声明方式对它们进行赋值。
	sub = num1 - num2
	return
}

func main() {
	// var age int = 10
	// fun(&age)
	a := fun
	// fmt.Printf("main----- %T,%T\n", a, fun)
	// a(10)
	test(1, 3.14, a)
	test(10, 3.14, fun)
	test01(10, 3.14, a)
	sub, sum := test02(10, 6)
	fmt.Println(sub, sum)
}
```



# 包

​		包是一个抽象概念，需要用 package 来声明一下。 导包，import 其实是导入 package 声明包文件所在的文件夹路径。

​		导入另一个包的操作是import 从 gopath 的src 下的路径开始算的，并且只有首字母大写的方法才能被别的包访问。

​		建议1\ package 进行包的声明，建议 声明的包和所在的文件夹同名；建议2\ main包是程序的入口包，一般main函数放在这个包下。

​		是哪一个包 ，要看 package 声明 叫什么名字，包就是什么名字，main函数一定要放在main包下。

​		**一个目录下不能有重复的函数**

​		同级别源文件的包的声明，必须一致！也就是说一个包其实对应一个文件夹。

​		![截屏2023-08-22 11.06.34](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-08-22%2011.06.34.jpg)

![截屏2023-08-22 11.12.42](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-08-22%2011.12.42.jpg)

# 匿名函数

```go
package main

import "fmt"

// 全局变量形式的匿名函数
var Fanc = func(num1 int, num2 int) int {
	return num1 * num2
}

func main() {
	//匿名函数，定义同时调用
	result := func(num1 int, num2 int) int {
		return num1 + num2
	}(10, 20)
	//匿名函数
	sub := func(num1 int, num2 int) int {
		return num1 - num2
	}

	fmt.Println(result)
	fmt.Println(sub(20, 10))
	fmt.Println(Fanc(2, 3))
}
```

# 闭包

```go
package main

import "fmt"

// 闭包 ，由返回的匿名函数和匿名函数以外的变量组成。
func getSum() func(int) int {
	var sum int = 0
	return func(num1 int) int {
		sum = sum + num1
		return sum
	}
}

func main() {
	f := getSum()
	fmt.Println(f(1))
	fmt.Println(f(2))
	fmt.Println(f(3))
}
```

# defer

```go
package main

import "fmt"

func main() {
	result := add(100, 200)
	fmt.Println(result)
}

func add(num1 int, num2 int) int {
	// defer 后面的内容会被压入一个栈中，直到本函数运行完才会运行
	// 由于是压入栈，先进后出。
	// defer语句一般用于 go语言在运行某段代码后释放资源。
	defer fmt.Println("num1=", num1)
	defer fmt.Println("num2=", num2)
	num1 += 10
	num2 += 20
	var sum int = num1 + num2
	fmt.Println(sum)
	return sum
}
```

# 字符串

```go
package main

import (
	"fmt"
	"strconv"
	"strings"
)

func main() {
	// utf-8 一个汉字3个字节
	str := "golang你好"
	fmt.Println(len(str))

	// 遍历字符串
	for i, value := range str {
		fmt.Printf("索引为：%d,值为: %c\n", i, value)
	}
	// 遍历字符串 2
	r := []rune(str)
	for i := 0; i < len(r); i++ {
		fmt.Printf("%c \n", r[i])
	}
	//字符串转整数
	num1, _ := strconv.Atoi("888")
	fmt.Println(num1)
	//整数转字符串
	str1 := strconv.Itoa(88)
	fmt.Println(str1)

	//统计子串数量
	count := strings.Count("golangisbestonegolang", "go")
	fmt.Println(count)
	//找子串索引
	fmt.Println(strings.Index("golangisbestonegolang", "go"))
	//字符串的替换
	str2 := strings.Replace("golangisbest", "golang", "python", -1)
	str3 := strings.Replace("golangisbest", "g", "python", 2)
	fmt.Println(str2)
	fmt.Println(str3)
	//切割
	arr := strings.Split("go-python-java", "-")
	fmt.Println(arr)
	// 大小写
	fmt.Println(strings.ToLower("ABC"))
	fmt.Println(strings.ToUpper("AbC"))
	// 去除左右两边。
	fmt.Println(strings.TrimSpace(" go and java "))
	fmt.Println(strings.Trim("~go and java~", "~"))
	// 判断开头
	fmt.Println(strings.HasPrefix("http://baidu.com", "http"))
}
```

# new

```go
	// new 分配内存，new返回值时一个对应类型的指针。
	num := new(int)
	fmt.Printf("num type is:%T,num value is:%v,num index is:%v,num指针指向的值是：%v",
		num, num, &num, *num)
```

# 异常捕获

```go
package main

import (
	"errors"
	"fmt"
)

func main() {
	test()
	fmt.Println("上面执行成功")
	fmt.Println("开始执行下面的逻辑")
	err := test2()
	if err != nil {
		fmt.Println("自定义错误：", err)
		panic(err) //panic 输出 参数后就不再向下执行了。
	}
	fmt.Println("test2上面执行成功")
	fmt.Println("test2开始执行下面的逻辑")
}

func test() {
	// 利用 defer 和 recover 来捕获异常(实际上是调用一个匿名函数)。
	defer func() {
		//调用 revover 内置函数
		err := recover()
		if err != nil {
			fmt.Println("异常被捕获")
			fmt.Println("err 是:", err)
		}
	}()
	num1 := 10
	num2 := 0
	result := num1 / num2
	fmt.Println(result)
}

func test2() (err error) {
	//自定义异常
	num1 := 10
	num2 := 0
	if num2 == 0 {
		return errors.New("除数不能为0")
	} else {
		result := num1 / num2
		fmt.Println(result)
		return nil
	}
}
```

# 数组

定义好的指针，默认值都是0；

```go
package main

import "fmt"

func main() {
	var scores [3]int // go 这样定义数组。
	/* 	scores[0] = 95
	   	scores[1] = 99
	   	scores[2] = 94 */
	var sum int = 0
	for i := 0; i < len(scores); i++ {
		fmt.Printf("please enter %d_th score\n", i+1)
		fmt.Scanln(&scores[i]) // 获取输入,对对应地址的值进行修改。

	}
	for i := 0; i < len(scores); i++ {
		sum += scores[i]
	}
	avg := sum / len(scores)
	fmt.Println(sum, avg)
}
```

![截屏2023-08-25 11.06.51](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-08-25%2011.06.51.jpg)

```go
	var scores [3]int = [3]int{1, 3, 9}
	var scores = [3]int{1, 2, 3}
	var scores = [...]int{1, 2, 3}
	var scores = [...]int{0: 99, 2: 100, 1: 1}
```

go 语言中的数组默认是值传递，和python等语言不一样，但是可以通过改变参数为指针，实现和python等语言一样的参数传递；

![截屏2023-08-25 16.05.21](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-08-25%2016.05.21.jpg)

![截屏2023-08-25 16.05.40](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-08-25%2016.05.40.jpg)

```go
package main

import "fmt"

func main() {
	var arr1 = [3]int{3, 6, 9}
	fmt.Printf("1类型是 %T\n", arr1) //1类型是 [3]int
	var arr2 = [6]int{1, 2, 3, 4, 5, 6}
	fmt.Printf("2类型是 %T\n", arr2) //2类型是 [6]int
	test(arr1)
	fmt.Println(arr1) //可见 arr1 的一号位置的值没有被test方法改变；
	// 注意，go和python等语言不通，数组的传递默认是值传递，
	// 而不是地址传递(引用传递)；

	test2(&arr1)
	fmt.Println(arr1)
}

func test(arr [3]int) {
	arr[0] = 100
}

// 把函数的参数改为指针，实现和python等语言一样的 数组 地址传递。
func test2(arr *[3]int) {
	(*arr)[0] = 200 // 需要先对 arr指针找到它对应的值，再按照第几个值去改变。
}
```

# 切片 

```go
package main

import "fmt"

func main() {
	var intarr = [6]int{1, 2, 3, 4, 5, 6}
	var slice []int = intarr[:3]
	fmt.Println(slice)
	fmt.Println("容量is：", cap(slice))
}
```

![截屏2023-08-25 16.24.25](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-08-25%2016.24.25.jpg)

注意切片并不是数组，而是一个单独的结构体；

由于 切片 存的是数组的地址，所以改变切片的值，原本数组的值也会跟着变；

## make

用 **make**  方法创建一个切片；

```go
package main

import "fmt"

func main() {
	// make 底层也是创建一个数组，对外不可见，不可以直接操作此数组，只能通过slice间接访问；
	slice := make([]int, 3, 20) // 切片的类型，长度，容量。
	fmt.Println(slice)
	fmt.Println("len is ", len(slice))
	slice[1] = 666
	fmt.Println(slice)
}
```

切片注意事项

【1】切片不用直接使用，需要引用一个数组 或者make提供一个空间供切片使用；

【2】切片可以继续切片；

【3】切片可以动态增长。

```go
package main

import "fmt"

func main() {
	var intarr [3]int = [3]int{1, 2, 3}
	slice := intarr[1:]
	fmt.Println(slice)
	// append 会新建一个数组，copy老数组的内容，在新数组上修改；
	slice2 := append(slice, 88, 99, 100) //追加三个元素
	fmt.Println("slice2 is :", slice2)
	fmt.Println("slice is :", slice)
	//如果想要对原始数组进行修改，只能通过重新赋值；
	slice = append(slice, 111, 222, 333)
	fmt.Println(slice)
}
```

```go
package main

import "fmt"

func main() {
	var a []int = []int{1, 2, 3}
	var b []int = make([]int, 10)
	//拷贝a到b
	copy(b, a)
	fmt.Println(b)
}
```

# map

就是python中的dict

```go
package main

import "fmt"

func main() {
	var a map[int]string         //定义map变量
	a = make(map[int]string, 10) //通过make初始化，不在make中指定size就默认只有一个键值对。
	a[123929] = "张三"
	a[2234234] = "李四"
	fmt.Println(a)
}
```

```go
package main

import "fmt"

func main() {
	var a map[int]string //定义map变量
	//通过make初始化，不在make中指定size就默认只有一个键值对，但是可以赋值时自动扩充。
	a = make(map[int]string, 10)
	a[123929] = "张三"
	a[2234234] = "李四"
	fmt.Println(a)

	// 方式2
	b := make(map[int]string)
	b[123123] = "zhansan"
	b[32143222] = "liudehua"
	b[1231234] = "zhouxinchi"
	fmt.Println(b)

	// 方式3
	c := map[int]string{
		200200: "张三",
		899898: "王五",
	}
	c[123982] = "刘德华"
	fmt.Println(c)

	//删除(go没有清空，只能遍历删除实现清空，或者make一个新的作它，原来的成为垃圾被回收。)
	delete(c, 200200)
	fmt.Println(c)

	//查找
	value, flag := b[123123] //flag 为true or false看找没找到。
	fmt.Println(value, flag)

	for k, v := range a {
		fmt.Printf("key is :%v, value is :%v \n", k, v)
	}
}
```

```go
package main

import (
	"fmt"
)

func main() {
	a := make(map[string]map[int]string)
	//赋值
	a["class1"] = make(map[int]string)
	a["class1"][200] = "小王"
	a["class1"][300] = "小红"

	a["class2"] = make(map[int]string)
	a["class2"][100] = "李辉"
	a["class2"][200] = "小宁"

	for k, v := range a {
		fmt.Println("班级是:", k)
		for k1, v1 := range v {
			fmt.Printf("学生学号是：%v,学生姓名是：%v\n", k1, v1)
		}
	}
}
```

# go的面向对象

go 不用类，用结构体。

## 结构体

```go
package main

import "fmt"

// 定义目标结构体
type Teacher struct {
	Name   string
	Age    int
	School string
}

func main() {
	// 创建老师结构体的实例、对象、变量。
	var ma Teacher  // 有点像变量的定义
	fmt.Println(ma) // 在未赋值时 默认是 { 0 } ,每个属性有初始值。
	ma.Name = "马云"
	ma.Age = 59
	ma.School = "杭州师范"
	fmt.Println(ma)
	fmt.Println(ma.Age + 10)
}
```

```go
package main

import "fmt"

// 定义目标结构体
type Teacher struct {
	Name   string
	Age    int
	School string
}

func main() {
	// 第二种方法 创建老师结构体的实例、对象、变量。
	var ma Teacher = Teacher{"赵云", 25, "白马义从"}
	fmt.Println(ma)
}
```

```go
package main

import "fmt"

// 定义目标结构体
type Teacher struct {
	Name   string
	Age    int
	School string
}

func main() {
	// 第三种方法 创建老师结构体的实例、对象、变量。
	// 通过new 一个结构体指针的方式。
	var t *Teacher = new(Teacher)
	(*t).Age = 100
	(*t).Name = "好人"
	fmt.Println(*t)

	// go 为了简化 写法，可以用一下方法赋值，但是实际上底层还是上面的指针方法。
	t.Name = "坏人"
	t.Age = 95
	fmt.Println(*t)
}
```

```go
package main

import "fmt"

// 定义目标结构体
type Teacher struct {
	Name   string
	Age    int
	School string
}

func main() {
	var t *Teacher = &Teacher{}
	//var t *Teacher = &Teacher{"赵云",25,"白马义从"}
	(*t).Name = "赵云"
	(*t).Age = 25
	t.School = "白马义从"
	fmt.Println(*t)
}
```

```go
package main

import "fmt"

// 结构体 和其它类型进行转换，需要有完全相同的字段。（名字，个数，类型）
type Teacher struct {
	Age int
}
type Tea Teacher
type Person struct {
	Age int
}

func main() {
	var t Teacher = Teacher{10}
	var p Person = Person{10}
	t = Teacher(p)
	fmt.Println(t)
	fmt.Println(p)

	// 结构体重新定义，取别名，只认最新数据类型，相互之间需要强转。
	var t1 Tea = Tea{100}
	t = Teacher(t1)
	fmt.Println(t1)
}
```

```go
package main

import "fmt"

type Student struct {
	Name string
	Age  int
}

func main() {
	var s1 Student = Student{"小王", 22}
	fmt.Println(s1)

	var s2 Student = Student{
		Name: "小红",
		Age:  33,
	}
	fmt.Println(s2)

	var s3 *Student = &Student{"小明", 30}
	fmt.Println(s3)

}
```

# 方法

```go
package main

import "fmt"

type Person struct {
	Age int
}

// 方法作用在指定的数据类型上。
// 结构体 传递参数给方法也是值传递。
func (p Person) test() {
	p.Age = 111
	fmt.Println(p.Age)
}

func main() {
	var p Person
	p.Age = 100
	p.test()
	fmt.Println(p.Age)
}
```

```go
package main

import "fmt"

type Person struct {
	Age int
}

func (p *Person) test() {
	(*p).Age = 2333 //也可以用程序员的简写 p.Age 其实底层还是(*p).Age;
	fmt.Printf("test 中p存好的内容是 %p\n", p)
	fmt.Println(p.Age) //输出原p的属性
}

func main() {
	var p Person
	p.Age = 100
	fmt.Printf("main 中p存放的地址是 %p\n", &p)
	(&p).test()
	fmt.Println(p.Age)
}
```

```go
package main

import "fmt"

type integer int

// 不用结构体 用其它类型试试。

func (i integer) print() {
	i = 10
	fmt.Println("print i = ", i)
}

func (i *integer) change() {
	*i = 30
	fmt.Println("change i = ", *i)
}

func main() {
	var i integer = 20
	fmt.Println("main i = ", i)
	i.print()
	fmt.Println("main i = ", i)
	// (&i).change()
	i.change() // go语言的简写形式，其实上面才是规范写法。
	fmt.Println("main i = ", i)
}
```

```go
package main

import "fmt"

type student struct {
	Name string
	Age  int
}

// 结构体类型 实现了 String方法 ,print此结构体就是自动输出String方法的返回值。
func (s *student) String() string {
	str := fmt.Sprintf("Name = %v,Age = %v", s.Name, s.Age)
	return str
}

func main() {
	stu := student{
		Name: "周文王",
		Age:  20,
	}
	// fmt.Println(stu.Name, stu.Age)
	fmt.Println(&stu)
}
```

函数 要严格按照 参数是 值还是指针来区分，但是方法不一样。

方法 接收者为 值类型，可以传入指针类型，接收者是 指针类型，也可以传入值类型。

```go
package main

import "fmt"

type student struct {
	Name string
	Age  int
}

func (s student) test01() {
	fmt.Println(s.Name, s.Age)
}

func (s *student) test02() {
	(*s).Name = "小黄"
	fmt.Println((*s).Name)
}

func main() {
	var s student = student{"小红", 100}
	s.test01()
	(&s).test01() // 虽然使用了指针类型调用，但是还是值传递形式。

	s.test02() // 虽然使用了值类型调用，但是还是指针传递形式。（会变）
	(&s).test02()
}
```

用工厂模式实现非首字母大写的结构体的跨包调用。

```go
// 被调用的包写如下
package model

type Student struct {
	Name string
	Age  int
}
type student struct {
	Name string
	Age  int
}

// 工厂模式
// 解决首字母小写，还能调用，“工厂模式”
func New_student(n string, a int) student {
	return student{n, a}
}

// 返回指针的工厂模式
func New_student_address(n string, a int) *student {
	return &student{n, a}
}
```

```go
// main包下写如下
package main

import (
	"demo15/model"
	"fmt"
)

func main() {
	// 跨包 创建结构体 实例
	// 要求另一个包内的 结构体首字母大写。
	var s model.Student = model.Student{"张三", 11}
	fmt.Println(s)

	//解决首字母小写，还能调用，“工厂模式”
	ss := model.New_student("李四", 12)
	fmt.Println(ss.Name, ss.Age)
	fmt.Printf("类型是：%T\n", ss)

	// 返回指针的工厂模式
	sss := model.New_student_address("王五", 13)
	fmt.Println((*sss).Name, (*sss).Age)
	fmt.Printf("类型是：%T\n", sss)
}
```

# 封装

```go
package model

import "fmt"

type person struct {
	Name string
	age  int // 不能别的包访问
}

// 定义工厂模式（函数） ，相当于构造器。
func NewPerson(name string) *person {
	return &person{
		Name: name,
	}
}

// 结构体指针的set方法，对age字段封装
func (p *person) SetAge(age int) {
	if age > 0 && age < 150 {
		p.age = age
	} else {
		fmt.Println("范围不正确.")
	}
}

// 结构体指针的get方法
func (p *person) GetAge() int {
	return p.age //同一个包下 直接访问。
}
```

```go
package main

import (
	"demo16/model"
	"fmt"
)

func main() {
	p := model.NewPerson("丽丽")
	p.SetAge(20)

	fmt.Println(p.Name)
	fmt.Println(p.GetAge())
	fmt.Println(p)
}
```

# 继承

```go
package main

import (
	"fmt"
)

// 定义结构体
type Animal struct {
	Age    int
	Weight float32
}

// 给结构体绑定上方法
func (a *Animal) Shout() {
	fmt.Println("wan wan wan ")
}

// 定义结构体
type Cat struct {
	// "结构体嵌入"实现继承，加入匿名结构体
  // 当一个结构体内嵌（嵌入）了其他结构体，而不指定字段的名称，这些嵌入的结构体称为匿名结构体。
	Animal
}

// 给结构体绑定上方法
func (c *Cat) scratch() {
	fmt.Println("闹人")
}

func main() {
	//创建cat结构体的实例
	cat := &Cat{}
	cat.Animal.Age = 3
	cat.Animal.Weight = 10.3
	cat.Animal.Shout()
	cat.scratch()
}
```

就近原则。

```go
package main

import (
	"fmt"
)

// 定义结构体
type Animal struct {
	Age    int
	Weight float32
}

// 给结构体绑定上方法
func (a *Animal) Shout() {
	fmt.Println("wan wan wan ")
}
func (a *Animal) showInfo() {
	fmt.Println("年龄是：", a.Age)
}

// 定义结构体
type Cat struct {
	// "结构体嵌入"实现继承，加入匿名结构体
	Animal
	Age int
}

func (c *Cat) showInfo() {
	fmt.Println("年龄是：", c.Age)
}

// 给结构体绑定上方法
func (c *Cat) scratch() {
	fmt.Println("闹人")
}

func main() {
	//创建cat结构体的实例
	cat := &Cat{}
	cat.Age = 9
	cat.Weight = 10.3
	cat.Animal.Age = 20
	cat.showInfo() // 就近原则
	cat.Animal.showInfo()
}
```

```go
// 多重继承,(不推荐)
package main

import (
	"fmt"
)

type A struct {
	a int
	b string
}
type B struct {
	c int
	d string
	a int
}
type C struct {
	A
	B
}

func main() {
	c := C{A{10, "aaa"}, B{20, "bbb", 30}}
	fmt.Println(c)
	fmt.Println(c.b)
	fmt.Println(c.A.a, c.B.a) // 有同名要区分。
}
```

结构体的匿名字段可以是基本数据类型。

```go
package main

import (
	"fmt"
)

type A struct {
	a int
	b string
}
type B struct {
	c int
	d string
	a int
}
type C struct {
	A
	B
	int // 把int 也当成一个匿名结构体，结构体的匿名字段可以是基本数据类型。
}

func main() {
	c := C{A{10, "aaa"}, B{20, "bbb", 30}, 888}
	fmt.Println(c)
	fmt.Println(c.b)
	fmt.Println(c.A.a, c.B.a) // 有同名要区分。
	fmt.Println(c.int)
}
```

```go
package main

import (
	"fmt"
)

type A struct {
	a int
	b string
}
type B struct {
	c int
	d string
	a int
}
type C struct {
	A
	B
	int // 把int 也当成一个匿名结构体，结构体的匿名字段可以是基本数据类型。
}
type C1 struct {
	*A // 传结构体的指针也是可以的。
	*B
	int // 把int 也当成一个匿名结构体，结构体的匿名字段可以是基本数据类型。
}
type D struct {
	a int
	b string
	c B // 组合模式。
}

func main() {
	c := C{A{10, "aaa"}, B{20, "bbb", 30}, 888}
	fmt.Println(c)
	fmt.Println(c.b)
	fmt.Println(c.A.a, c.B.a) // 有同名要区分。
	fmt.Println(c.int)

	c1 := C1{&A{10, "aaa"}, &B{20, "bbb", 30}, 888}
	fmt.Println(c1)
	fmt.Println(c1.A)
	fmt.Println(*c1.A)

	d := D{10, "ooo", B{10, "bbb", 100}}
	fmt.Println(d)
	fmt.Println(d.c.d)
}
```

# 接口（借尸还魂）

```go
package main

import "fmt"

// 接口的定义s
type SayHello interface {
	// 声明没有实现的方法。
	sayHello()
}

// 接口的实现
// 先定义一个结构体
type Chinese struct {
}

// 再 用一个绑定结构体的方法来实现 上面的接口。
// 如果想要实现某一个接口，需要把此接口内的所有的方法都实现。
func (person Chinese) sayHello() {
	fmt.Println("你好")
}

type American struct {
}

func (person American) sayHello() {
	fmt.Println("hi")
}

// 定义一个函数，用来接收 具备SayHello接口能力的变量。
// golang 实现接口是基于方法的，而不是基于接口的。
func greet(s SayHello) {
	s.sayHello()
}

func main() {
	c := Chinese{}
	a := American{}
	greet(c)
	greet(a)
}
```

![截屏2023-09-02 10.59.52](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-02%2010.59.52.jpg)

![截屏2023-09-02 11.05.21](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-02%2011.05.21.jpg)

一个结构体实现多个接口。

```go
package main

import "fmt"

type AInterface interface {
	a()
}
type BInterface interface {
	b()
}
type Stu struct {
}

func (s Stu) a() {
	fmt.Println("aaaa")
}
func (s Stu) b() {
	fmt.Println("bbbb")
}

func main() {
	var s Stu
	var a AInterface = s
	var b BInterface = s
	a.a()
	b.b()
}
```

```go
package main

import "fmt"

type AInterface interface {
	a()
}
type BInterface interface {
	b()
}
type CInterface interface {
	AInterface
	BInterface
	c()
}

type Stu struct {
}

func (s Stu) a() {
	fmt.Println("aaaa")
}
func (s Stu) b() {
	fmt.Println("bbbb")
}
func (s Stu) c() {
	fmt.Println("ccc")
}

func main() {
	var s Stu
	var a AInterface = s
	var b BInterface = s
	a.a()
	b.b()

	var c CInterface = s
	c.c( )
}
```

空接口 没有实现任何方法，所以可以视为 任意类型都实现了空接口。

```go
type E interface {
}

func main() {
	var s Stu
	var a AInterface = s
	var b BInterface = s
	a.a()
	b.b()

	var c CInterface = s
	c.c()

	var e E = s // 空接口。
	fmt.Println(e)
	// 另一种空接口 实现。
	var ee interface{} = s
	fmt.Println(ee)
}
```

# 多态

golang的多态 也是通过 接口 实现的。

```go
package main

import "fmt"

// 接口的定义s
type SayHello interface {
	// 声明没有实现的方法。
	sayHello()
}

// 接口的实现
// 先定义一个结构体
type Chinese struct {
}

// 再 用一个绑定结构体的方法来实现 上面的接口。
// 如果想要实现某一个接口，需要把此接口内的所有的方法都实现。
func (person Chinese) sayHello() {
	fmt.Println("你好")
}

type American struct {
}

func (person American) sayHello() {
	fmt.Println("hi")
}

// 定义一个函数，用来接收 具备SayHello接口能力的变量。
// golang 实现接口是基于方法的，而不是基于接口的。
func greet(s SayHello) {
	s.sayHello()
}

func main() {
	c := Chinese{}
	a := American{}
	greet(c)
	greet(a)
}
```

![截屏2023-09-02 16.06.40](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-02%2016.06.40.jpg)

# 断言

```go
package main

import "fmt"

// 接口的定义s
type SayHello interface {
	// 声明没有实现的方法。
	sayHello()
}

// 接口的实现
// 先定义一个结构体
type Chinese struct {
	name string
}

// 再 用一个绑定结构体的方法来实现 上面的接口。
// 如果想要实现某一个接口，需要把此接口内的所有的方法都实现。
func (person Chinese) sayHello() {
	fmt.Println("你好")
}

// 一个接口外的 方法。
func (person Chinese) niu() {
	fmt.Println("扭秧歌")
}

type American struct {
	name string
}

func (person American) sayHello() {
	fmt.Println("hi")
}

// 定义一个函数，用来接收 具备SayHello接口能力的变量。
// golang 实现接口是基于方法的，而不是基于接口的。
func greet(s SayHello) {
	s.sayHello()
	// 断言
	var ch Chinese = s.(Chinese) // 看s是否能转为Chinese类型，并赋给ch变量。
	ch.niu()
}

func main() {
	c := Chinese{"陈"}
	// a := American{"宇"}
	greet(c)
	// greet(a)
}
```

```go
package main

import "fmt"

// 接口的定义s
type SayHello interface {
	// 声明没有实现的方法。
	sayHello()
}

// 接口的实现
// 先定义一个结构体
type Chinese struct {
	name string
}

// 再 用一个绑定结构体的方法来实现 上面的接口。
// 如果想要实现某一个接口，需要把此接口内的所有的方法都实现。
func (person Chinese) sayHello() {
	fmt.Println("你好")
}

// 一个接口外的 方法。
func (person Chinese) niu() {
	fmt.Println("扭秧歌")
}

type American struct {
	name string
}

func (person American) sayHello() {
	fmt.Println("hi")
}

// 定义一个函数，用来接收 具备SayHello接口能力的变量。
// golang 实现接口是基于方法的，而不是基于接口的。
func greet(s SayHello) {
	s.sayHello()
	// 断言
	ch, flag := s.(Chinese) // 看s是否能转为Chinese类型，并赋给ch变量。
	// if ch, flag := s.(Chinese); flag == true { //这样写；前是定义，；后判断。
  if flag == true {
		ch.niu()
	} else {
		fmt.Println("不会扭秧歌")
	}
}

func main() {
	// c := Chinese{"陈"}
	a := American{"宇"}
	// greet(c)
	greet(a)
}
```

# 文件

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	//打开
	file, err := os.Open("/Users/chenyushao/Desktop/golang_draft/test.txt")
	if err != nil {
		fmt.Println("文件打开失败,对应错误为：", err)
	} else {
		fmt.Println("文件的指针是：", file)
	}

	//一系列操作

	//关闭文件
	err = file.Close()
	if err != nil {
		fmt.Println("关闭失败")
	} else {
		fmt.Println("关闭成功")
	}
}
```

![截屏2023-09-04 11.22.22](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-04%2011.22.22.jpg)

带缓冲的读取，4096.

```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"os"
)

func main() {
	//打开
	file, err := os.Open("/Users/chenyushao/Desktop/golang_draft/test.txt")
	if err != nil {
		fmt.Println("文件打开成功。")
	}
	//函数退出时，关闭，防治内存泄漏。
	defer file.Close()

	//创建流
	reader := bufio.NewReader(file)
	//读取,死循环读取。
	for {
		str, err := reader.ReadString('\n')
		if err == io.EOF {
			fmt.Println("读取完毕")
			break
		}
		fmt.Println(str)
	}

	fmt.Println("文件读物成功，且读取完毕。")
}
```

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	// 写入文件。
	//打开文件
	file, err := os.OpenFile("/Users/chenyushao/Desktop/golang_draft/test.txt", os.O_APPEND|os.O_RDWR|os.O_CREATE, 0751)
	if err != nil {
		fmt.Println("打开文件失败，", err)
		return
	}
	//及时关闭资源
	defer file.Close()

	//写入
	//io流，先写入缓冲区。
	writer := bufio.NewWriter(file)
	writer.WriteString("hello ")
	for i := 0; i < 10; i++ {
		writer.WriteString("重复")
	}
	// 流带缓冲区，刷新数据，把管子清一下。(真正写入到文件)
	writer.Flush()
}
```

# 进程、线程、协程、管道

一个运行的程序就是一个 进程；

一个进程中可以有一个或者多个线程；

一个cpu运行多个线程，就是伪多线程，实际上是一个cpu在多个线程片段之间切换，只是切换的速度比较快，切换不同的线程涉及系统层面的开销；

协程（goroutine） 就是 微线程，本质是 对单一线程的优化（协程是单线程的并发）。

![截屏2023-09-06 10.55.26](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-06%2010.55.26.jpg)



```go
package main

import (
	"fmt"
	"strconv"
	"time"
)

func test() { // 准备用作协程的函数（协程属于函数调用层面）
	for i := 1; i <= 10; i++ {
		fmt.Println("协程 goroutine:" + strconv.Itoa(i))
		time.Sleep(time.Second * 1)
	}
}

func main() { // 主线程
	go test() // go 中开启协程。

	for i := 1; i <= 10; i++ {
		fmt.Println("主线程:" + strconv.Itoa(i))
		time.Sleep(time.Second * 1)
	}
}

// 运行结果
主线程:1
协程 goroutine:1
协程 goroutine:2
主线程:2
主线程:3
协程 goroutine:3
主线程:4
协程 goroutine:4
协程 goroutine:5
主线程:5
协程 goroutine:6
主线程:6
主线程:7
协程 goroutine:7
协程 goroutine:8
主线程:8
协程 goroutine:9
主线程:9
协程 goroutine:10
主线程:10
```

![截屏2023-09-06 11.12.39](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-06%2011.12.39.jpg)

主死随从

```go
// 主线程结束了，协程也会跟着结束
package main

import (
	"fmt"
	"strconv"
	"time"
)

func test() { // 准备用作协程的函数（协程属于函数调用层面）
	for i := 1; i <= 100; i++ {
		fmt.Println("协程 goroutine:" + strconv.Itoa(i))
		time.Sleep(time.Second * 1)
	}
}

func main() { // 主线程
	go test() // go 中开启协程。

	for i := 1; i <= 10; i++ {
		fmt.Println("主线程:" + strconv.Itoa(i))
		time.Sleep(time.Second * 1)
	}
}
```

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	// 用匿名函数 和 外部变量组成 闭包
	for i := 0; i < 10; i++ {
		//启动协程
		go func() {
			fmt.Println(i)
		}()
	}
	time.Sleep(time.Second * 2)
}
// 以上代码 会让10个协程 并发， i变量不确定运行到多少时，被协程的匿名函数用上。
10
10
10
10
10
10
10
10
6
10
```

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	// 用匿名函数 和 外部变量组成 闭包
	for i := 0; i < 10; i++ {
		//启动协程
		go func(num int) {
			fmt.Println(num)
		}(i)
	}
	time.Sleep(time.Second * 2)
}
// 循环每生成一个协程的匿名函数时，把此时的i以参数形式，传入匿名函数。
1
0
9
2
6
8
7
4
3
5
```

```go
package main

import (
	"fmt"
	"sync"
)

var wg sync.WaitGroup // 只需要定义，无需赋值。计数器
func main() {
	// 用匿名函数 和 外部变量组成 闭包
	for i := 0; i < 10; i++ {
		wg.Add(1) // 注意这里wg add的次数，要保证和 协程的个数一致。
		//启动协程
		go func(num int) {
      defer wg.Done()
			fmt.Println(num)
		}(i)
	}
	wg.Wait()
}
0
9
5
6
7
8
2
1
3
4
```

```go
package main

import (
	"fmt"
	"sync"
)

var num int
var wg sync.WaitGroup //定义不需要赋值。
func add() {
	defer wg.Done()
	for i := 0; i < 10000; i++ {
		num = num + 1
	}
}

func sub() {
	defer wg.Done()
	for i := 0; i < 10000; i++ {
		num = num - 1
	}
}

func main() {
	wg.Add(2)
	//启动协程
	go add()
	go sub()
	wg.Wait()
	fmt.Println(num)
}  
//结果 
1045
```

![截屏2023-09-07 10.34.18](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-07%2010.34.18.jpg)

多个协程之间 交替执行导致了问题，协程并发争抢资源。

用 锁 可以解决上面的问题，但是不推荐,因为性能低。

```go
package main

import (
	"fmt"
	"sync"
)

var num int
var wg sync.WaitGroup //定义不需要赋值。
// 加入互斥锁 mutex lock
var lock sync.Mutex

func add() {
	defer wg.Done()
	for i := 0; i < 10000; i++ {
		// 加锁
		lock.Lock()
		num = num + 1
		// 解锁
		lock.Unlock()
	}
}

func sub() {
	defer wg.Done()
	for i := 0; i < 10000; i++ {
		// 加锁
		lock.Lock()
		num = num - 1
		// 解锁
		lock.Unlock()
	}
}

func main() {
	wg.Add(2)
	//启动协程
	go add()
	go sub()
	wg.Wait()
	fmt.Println(num)
}
```

读多写少，推荐用读写锁RWLock。

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var num int
var wg sync.WaitGroup //定义不需要赋值。
// 加入读写锁 RWMutex lock
var lock sync.RWMutex

func add() {
	defer wg.Done()
	for i := 0; i < 10; i++ {
		// 加锁
		lock.RLock() // 读之间不锁，只针对写锁起来。
		fmt.Println("开始读")
		num = num + 1
		time.Sleep(time.Second * 1)
		// 解锁
		lock.RUnlock()
		fmt.Println("读完了")
	}
}

func sub() {
	defer wg.Done()
	for i := 0; i < 2; i++ {
		// 加锁
		lock.Lock() // 默认的Lock就是写锁。
		fmt.Println("开始写")
		num = num - 1
		time.Sleep(time.Second * 1)
		// 解锁
		lock.Unlock()
		fmt.Println("写完了")
	}
}

func main() {
	wg.Add(2) // add 的计数要和 协程数量一致。
	//启动协程
	go add()
	go sub()
	wg.Wait()
	fmt.Println(num)
}
```

## 管道

```go
package main

import "fmt"

func main() {
	// 管道 声明
	var intChan chan int
	// 通过make初始化，此管道可以放3个int数据。
	intChan = make(chan int, 3)
	// 证明管道是 引用类型
	fmt.Printf("intChan的值是：%v \n", intChan)

	// 向管道放数据
	intChan <- 10
	num := 20
	intChan <- num
	intChan <- 50
	// intChan <- 500 // 超了会死锁。

	// 管道内读取数据
	num1 := <-intChan
	num2 := <-intChan
	num3 := <-intChan
	fmt.Println(num1, num2, num3)

	fmt.Printf("管道实际长度：%v,管道容量是 %v", len(intChan), cap(intChan))
}
```

管道关闭以后 读取可以，只是不能再写入了。如果写入超过容量，会死锁。

```go
package main

import "fmt"

func main() {
	// 管道 声明
	var intChan chan int
	// 通过make初始化，此管道可以放3个int数据。
	intChan = make(chan int, 100)
	for i := 0; i < 10; i++ {
		intChan <- i
	}
	close(intChan)
	//遍历 前记得关闭管道。
	for v := range intChan {
		fmt.Println("value = ", v)
	}
}
```

![截屏2023-09-07 22.14.58](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-07%2022.14.58.jpg)

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func writeData(intChan chan int) {
	defer wg.Done()
	for i := 1; i <= 50; i++ {
		intChan <- i
		fmt.Println("写入的数据为：", i)
		time.Sleep(time.Second)
	}
	//写完以后关闭管道
	close(intChan)
}

func readData(intChan chan int) {
	defer wg.Done()
	for v := range intChan {
		fmt.Println("读的数据为：", v)
		time.Sleep(time.Second)
	}
}

var wg sync.WaitGroup // 用协程计数器 做一个阻塞。

func main() { //主线程
	//写的协程 和 读协程 共同操作一个管道。
	intChan := make(chan int, 50)

	wg.Add(2) // 用到的是两个协程。
	//开启读写协程
	go writeData(intChan)
	go readData(intChan)
	wg.Wait()
}
```

管道可以声明 只读或者只写。

```go
package main

import (
	"fmt"
)

func main() { //主线程
	// 正常情况下，管道是双向的，可读可写。
	// var intChan chan int

	// 声明为只写
	var intChan2 chan<- int // 管道有 只读性质。而不是一种新类型
	intChan2 = make(chan int, 5)
	intChan2 <- 10
	// num := <-intChan2 // 有只写性质后，这样的读管道就非法了。
	fmt.Println("intChan2", intChan2)

	// 声明为只读
	var intChan3 <-chan int
	if intChan3 != nil {
		num1 := <-intChan3
		fmt.Println("num1", num1)
	}
	intChan3 <- 20 // 有只读性质后，这样的写管道就非法了。
}
```

有写没有读，管道就会deadlock 死锁。

但是写的快，读得慢，是不会死锁的。

## select

select 解决多个管道的选择问题，又叫“多路复用”

```go
package main

import (
	"fmt"
	"time"
)

func main() { //主线程
	//管道1
	intChan := make(chan int, 1)
	go func() { // 通过一个匿名函数往管道1内 写。
		time.Sleep(time.Second * 5)
		intChan <- 10
	}()
	//管道2
	stringChan := make(chan string, 1)
	go func() { // 通过一个匿名函数往管道2内 写。
		time.Sleep(time.Second * 2)
		stringChan <- "msbgolang"
	}()

	// fmt.Println(<-intChan) // 本身 取数据就是 阻塞。
	// select 可以公平的从多个管道中按照时间长短， 选择一个来阻塞并执行。
	// 像以下就是输出“stringChan: msbgolang” 因为管道2的那个写入的协程时间短一些。
	select {
	case v := <-intChan: // 语句写法是固定的。case 后跟一个读管道操作。
		fmt.Println("intChan：", v)
	case v := <-stringChan:
		fmt.Println("stringChan:", v)
	}
}
```

```go
package main

import (
	"fmt"
	"time"
)

func main() { //主线程
	//管道1
	intChan := make(chan int, 1)
	go func() { // 通过一个匿名函数往管道1内 写。
		time.Sleep(time.Second * 5)
		intChan <- 10
	}()
	//管道2
	stringChan := make(chan string, 1)
	go func() { // 通过一个匿名函数往管道2内 写。
		time.Sleep(time.Second * 2)
		stringChan <- "msbgolang"
	}()

	// fmt.Println(<-intChan) // 本身 取数据就是 阻塞。
	// select 可以公平的从多个管道中按照时间长短， 选择一个来阻塞并执行。
	// 像以下就是输出“stringChan: msbgolang” 因为管道2的那个写入的协程时间短一些。
	select {
	case v := <-intChan: // 语句写法是固定的。case 后跟一个读管道操作。
		fmt.Println("intChan：", v)
	case v := <-stringChan:
		fmt.Println("stringChan:", v)
	default: //default 可以防止case 的阻塞被执行。
		fmt.Println("防止select被阻塞")
	}
}
```

主动捕获异常defer+recover，来让协程出问题的时候，其它协程和主线程正常运行。

```go
package main

import (
	"fmt"
	"time"
)

func printNum() {
	for i := 0; i < 10; i++ {
		fmt.Println(i)
	}
}

func devide() {
	// 捕获异常。
	defer func() {
		err := recover()
		if err != nil {
			fmt.Println("devide() 出现错误:", err)
		}
	}()

	num1 := 1
	num2 := 0
	result := num1 / num2
	fmt.Println("devide:", result)
}

func main() { //主线程
	//启动两个goroutine
	go printNum()
	go devide()

	// 让主线程等一等
	time.Sleep(time.Second * 4)
}
```

# 网络

![截屏2023-09-09 10.41.22](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-09%2010.41.22.jpg)

## 传输层：TCP 协议三次握手，四次挥手

![截屏2023-09-09 10.43.02](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-09%2010.43.02.jpg)

![截屏2023-09-09 10.44.42](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-09%2010.44.42.jpg)

## 传输层：UDP 协议（不可靠）

UDP协议可能丢包；

数据切成一个一个的小数据包 发送给服务器。服务器也接收一个一个的小数据包。

```go
// 客户端
package main

import (
	"fmt"
	"net" // 网络需要的包。
)

func main() {
	fmt.Println("客户端启动")
	// 创建客户端(指定服务器端IP 和 PROT)
	conn, err := net.Dial("tcp", "127.0.0.1:8888")
	if err != nil {
		fmt.Println("客户端链接失败")
		return
	}
	fmt.Println("客户端连接成功,conn:", conn)
}
```

```go
// 服务器端
// 测试时，要先启动服务器端，因为服务器端要先启动 等待客户端发消息过来。

package main

import (
	"fmt"
	"net" // 网络需要的包。
)

func main() {
	fmt.Println("服务器端启动")
	// 监听
	listen, err := net.Listen("tcp", "127.0.0.1:8888")
	if err != nil {
		fmt.Println("监听失败,err:", err)
		return
	}
	// 监听成功后，等待客户端连接
	conn, err2 := listen.Accept()
	if err2 != nil { // 客户端等待失败
		fmt.Println("客户端等待失败，err2", err2)
		return
	} else {
		fmt.Printf("连接成功,%v\n,接收到的客户端远程地址是:%v\n", conn, conn.RemoteAddr().String())
	}
}
/* 在实际连接时，服务器会分配一个临时的端口给客户端，
而不是使用监听的端口。这是因为在传输层（TCP 协议）中，
客户端和服务器之间的通信是通过客户端随机 分配的一个临时端口与服务器的监听端口建立连接的。*/
```

完整版

```go
// 服务器
// 测试时，要先启动服务器端，因为服务器端要先启动 等待客户端发消息过来。

package main

import (
	"fmt"
	"net" // 网络需要的包。
	"os"
)

func process(conn net.Conn) {
	defer conn.Close() // 链接用完记得关闭。
	for {
		// 创建一个切片,将读入的数据 放进切片中。
		buf := make([]byte, 1024)
		n, err := conn.Read(buf)
		if err != nil {
			return
		}
		if string(buf[0:n]) == "quit\n" {
			fmt.Println("接收到客户端注销信号,现在执行.")
			os.Exit(1)
		}
		fmt.Println(string(buf[0:n]))
	}
}

func main() {
	fmt.Println("服务器端启动")
	// 监听
	listen, err := net.Listen("tcp", "127.0.0.1:8888")
	if err != nil {
		fmt.Println("监听失败,err:", err)
		return
	}
	for {
		// 监听成功后，等待客户端连接
		conn, err2 := listen.Accept()
		if err2 != nil { // 客户端等待失败
			fmt.Println("客户端等待失败，err2", err2)
			return
		} else {
			fmt.Printf("连接成功,%v\n,接收到的客户端远程地址是:%v\n", conn, conn.RemoteAddr().String())
		}

		// 准备一个协程，此协程处理不同客户端请求。
		go process(conn)
	}
}
```

```go
package main

import (
	"bufio"
	"fmt"
	"net" // 网络需要的包。
	"os"
)

func main() {
	fmt.Println("客户端启动")
	// 创建客户端(指定服务器端IP 和 PROT)
	conn, err := net.Dial("tcp", "127.0.0.1:8888")
	if err != nil {
		fmt.Println("客户端链接失败")
		return
	}
	fmt.Println("客户端连接成功,conn:", conn)
	for {
		reader := bufio.NewReader(os.Stdin)
		str, err := reader.ReadString('\n')
		if err != nil {
			fmt.Println("终端输入失败,err", err)
		}
		if str == "quit\n" {
			_, err := conn.Write([]byte(str)) // 这里需要强转一下。
			if err != nil {
				fmt.Println("写入失败")
			}
			fmt.Println("手动退出客户端")
			return
		}
		// 将str发送给服务器
		n, err := conn.Write([]byte(str)) // 这里需要强转一下。
		if err != nil {
			fmt.Println("写入失败")
		}
		fmt.Printf("客户端数据发送成功，一共发送了%d字节的数据。", n)
	}
}
```

# 反射

放射能够 在 运行时动态获取变量信息。

![截屏2023-09-10 11.29.05](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-10%2011.29.05.jpg)

```go
package main

import (
	"fmt"
	"reflect"
)

// 利用一个函数，函数的参数为空接口；
// 空接口没有方法，可以视为所有类型都实现了空接口。
func testReflect(i interface{}) {
	// 1.调用TypeOf函数，返回reflect.Type类型
	reType := reflect.TypeOf(i)
	fmt.Println("reType:", reType)
	reValue := reflect.ValueOf(i)
	fmt.Println("Value:", reValue)

	// num1 := 2
	// reValue 是 Value 类型的结构体 不是int类型
	num2 := 50 + reValue.Int() //num1
	fmt.Println(num2)

	// 原路返回
	// reValue 转为空接口(其实是转为保管在空接口中的类型)：
	i2 := reValue.Interface()
	// 利用断言，看看能不能转为int类型。
	n := i2.(int)
	fmt.Println(n + 10)
}

func main() {
	// 对基础数据类型进行反射
	var num int = 100
	testReflect(num)
}
```

![截屏2023-09-10 12.27.40](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-10%2012.27.40.jpg)

```go
package main

import (
	"fmt"
	"reflect"
)

// 利用一个函数，函数的参数为空接口；
// 空接口没有方法，可以视为所有类型都实现了空接口。
func testReflect(i interface{}) {
	// 1.调用TypeOf函数，返回reflect.Type类型
	reType := reflect.TypeOf(i)
	fmt.Println("reType:", reType)
	reValue := reflect.ValueOf(i)
	fmt.Println("Value:", reValue)

	// 原路返回
	// reValue 转为空接口(其实是转为保管在空接口中的类型)：
	i2 := reValue.Interface()
	// 利用断言，看看能不能转为int类型。
	n, flag := i2.(Student)
	if flag == true {
		fmt.Printf("名字是：%v,年龄是：%v", n.Name, n.Age)
	}
}

type Student struct {
	Name string
	Age  int
}

func main() {
	// 对基础数据类型进行反射
	var num int = 100
	testReflect(num)

	// 对结构体反射
	stu := Student{
		Name: "丽丽",
		Age:  18,
	}
	testReflect(stu)
}
```

```go
package main

import (
	"fmt"
	"reflect"
)

// 利用一个函数，函数的参数为空接口；
// 空接口没有方法，可以视为所有类型都实现了空接口。
func testReflect(i interface{}) {
	// 1.调用TypeOf函数，返回reflect.Type类型
	reType := reflect.TypeOf(i)
	fmt.Println("reType:", reType)
	reValue := reflect.ValueOf(i)
	fmt.Println("Value:", reValue)

	// 获取变量类别(类别比 类型的范围要大一些)
	k1 := reType.Kind()  // 方式一
	k2 := reValue.Kind() // 方式二
	fmt.Println(k1, k2, k1 == k2, "look this.")

	// 原路返回
	// reValue 转为空接口(其实是转为保管在空接口中的类型)：
	i2 := reValue.Interface()
	// 利用断言，看看能不能转为int类型。
	n, flag := i2.(Student)
	if flag == true {
		fmt.Printf("名字是：%v,年龄是：%v", n.Name, n.Age)
		// 获取变量类型
		fmt.Printf("\n类型是:%T", n)
	}
}

type Student struct {
	Name string
	Age  int
}

func main() {
	// 对基础数据类型进行反射
	var num int = 100
	testReflect(num)

	// 对结构体反射
	stu := Student{
		Name: "丽丽",
		Age:  18,
	}
	testReflect(stu)
}
```

通过反射修改变量

```go
package main

import (
	"fmt"
	"reflect"
)

// 利用一个函数，函数的参数为空接口；
// 空接口没有方法，可以视为所有类型都实现了空接口。
func testReflect(i interface{}) {
	reValue := reflect.ValueOf(i)
	fmt.Println("Value:", reValue)

	// 通过Elen \SetInt 来改变值：
	// SetInt 设置v的持有值。如果v的Kind不是Int、Int8、Int16、Int32、Int64之一或者v.CanSet()返回假，会panic。
	// Elem Elem返回v持有的接口保管的值的Value封装，或者v持有的指针指向的值的Value封装。如果v的Kind不是Interface或Ptr会panic；如果v持有的值为nil，会返回Value零值。
	reValue.Elem().SetInt(101)
}

type Student struct {
	Name string
	Age  int
}

func main() {
	var num int = 100
	testReflect(&num) // 想要改变值 ，要传递指针。
	fmt.Println(num)
}
```

```go
package main

import (
	"fmt"
	"reflect"
)

type Student struct {
	Name string
	Age  int
}

func (s Student) CPrint() {
	fmt.Println("调用print方法，学生名字是：", s.Name)
}

func (s Student) AGetSum(n1, n2 int) int {
	return n1 + n2
}

func (s Student) BSet(name string, age int) {
	s.Name = name
	s.Age = age
}

// 定义一个函数来 操作结构体 进行反射操作
func TestStudentStruct(a interface{}) {
	// 将a转成 reflect 的value 类型
	val := reflect.ValueOf(a)
	fmt.Println(val)

	// 操作结构体内部字段
	n1 := val.NumField()
	fmt.Println("结构体内部字段数为：", n1)
	for i := 0; i < n1; i++ {
		fmt.Printf("第%d个字段的值是：%v\n", i, val.Field(i))
	}

	// 操作结构体内部方法
	n2 := val.NumMethod()
	fmt.Println(n2)
	// value 的 method 只能访问到 外部能访问的方法，也就是首字母大写的。
	val.Method(2).Call(nil) //Call 的参数就是 结构体对应方法 输入的参数，上面的正好没有参数。
	// 定义Value 的切片,也就是一个切片，元素都是 reflect的Value类型。
	var params []reflect.Value
	params = append(params, reflect.ValueOf(10))
	params = append(params, reflect.ValueOf(20))
	result := val.Method(0).Call(params) // Method 按照 ASCII 码取方法。 
	fmt.Println("返回值是：", result[0], result)
}

func main() {
	s := Student{
		Name: "丽丽",
		Age:  18,
	}
	// 调用
	TestStudentStruct(s)
}
```

```go
package main

import (
	"fmt"
	"reflect"
)

type Student struct {
	Name string
	Age  int
}

func (s Student) CPrint() {
	fmt.Println("调用print方法，学生名字是：", s.Name)
}

func (s Student) AGetSum(n1, n2 int) int {
	return n1 + n2
}

func (s Student) BSet(name string, age int) {
	s.Name = name
	s.Age = age
}

// 定义一个函数来 操作结构体 进行反射操作
func TestStudentStruct(a interface{}) {
	// 将a转成 reflect 的value 类型
	val := reflect.ValueOf(a)
	fmt.Println(val)

	n := val.Elem().NumField()
	fmt.Println(n)

	// 修改字段的值
	val.Elem().Field(0).SetString("张三")
}

func main() {
	s := Student{
		Name: "丽丽",
		Age:  18,
	}
	// 调用
	TestStudentStruct(&s) // 修改 要传 指针。
	fmt.Println(s)
}
```

# 协程管道练习

![截屏2023-09-18 10.44.04](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-18%2010.44.04.jpg)

```go
package main

import (
	"fmt"
)

var intChan chan int = make(chan int, 10000)

func main() {
	var printChan chan int = make(chan int, 10000)
	var exitChan chan bool = make(chan bool, 8)
	go initChan(10000)

	for i := 0; i <= 8; i++ {
		go isPrime(intChan, printChan, exitChan)
	}

	go func() {
		for i := 0; i < 8; i++ {
			<-exitChan
		}
		close(printChan) // 在这里关闭 上面的存放输出数据的管道。
	}()

	for {
		res, ok := <-printChan
		if !ok {
			break
		}
		fmt.Println("素数是：", res)
	}

}

func initChan(num int) {
	for i := 1; i < num; i++ {
		intChan <- i
	}
	close(intChan) // 写完后，不关闭掉，再读的时候可能有死锁问题。
}

func isPrime(intChan chan int, printChan chan int, exitChan chan bool) {
	var flag bool
	for {
		num, ok := <-intChan
		flag = true
		if !ok {
			break
		}

		for j := 2; j < num; j++ {
			if num%j == 0 {
				flag = false
				continue
			}
		}
		if flag {
			printChan <- num
			// fmt.Println(num, "是素数.")
		}
	}
	exitChan <- true
}
```

##  label 和 select 管道

​		管道写完都要 close，不然读的时候可能会死锁错误，如果不想总是用这个 锁，"每次操作管道"都要使用 label 配合 select 选择管道，select 的default 返回值 赋值给了label。

`label: for {select {case := <-chanName:`
` case := <-chanName2:` 
` default returnValue }}`

```go
package main

import (
	"fmt"
)

var intChan chan int = make(chan int, 10000)

func main() {
	var printChan chan int = make(chan int, 10000)
	var exitChan chan bool = make(chan bool, 8)
	go initChan(10000)

	for i := 0; i <= 8; i++ {
		go isPrime(intChan, printChan, exitChan)
	}

	go func() {
		for i := 0; i < 8; i++ {
			<-exitChan
		}
		// 用 label 配合 select 不再用 close关闭管道。
		//close(printChan) // 在这里关闭 上面的存放输出数据的管道。
	}()
label:
	for {
		select {
		case res := <-printChan:
			fmt.Println("素数是：", res)
		default:
			break label
		}
	}

}

func initChan(num int) {
	for i := 1; i < num; i++ {
		intChan <- i
	}
	close(intChan) // 写完后，不关闭掉，再读的时候可能有死锁问题。
}

func isPrime(intChan chan int, printChan chan int, exitChan chan bool) {
	var flag bool
label:
	for {
		select {
		case num := <-intChan:
			flag = true
			for j := 2; j < num; j++ {
				if num%j == 0 {
					flag = false
					continue
				}
			}
			if flag {
				printChan <- num
				// fmt.Println(num, "是素数.")
			}
		default:
			break label
		}
	}
	fmt.Println("协程已经结束")
	exitChan <- true
}
```

![截屏2023-09-19 11.15.44](go%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2023-09-19%2011.15.44.jpg)

```go
package main

import (
	"fmt"
	"strconv"
	"time"
)

func main() {
	fmt.Println()
	storageChan := make(chan Product, 100)
	shopChan := make(chan Product, 100)
	exitChan := make(chan bool, 1)
	// 通过一个循环 开启多协程。
	for i := 0; i < 9; i++ { // 但是这样一开以后，九个都会往管道写，每个都从10 开始循环，所以会被多个10填满管道。
		go Producer(storageChan, 10)
	}
	// go Producer(storageChan, 10)
	go Logistics(storageChan, shopChan)
	go Consumer(shopChan, 10, exitChan)
	if <-exitChan {
		return
	}
}

type Product struct {
	Name string
}

// storageChan 是只写。
func Producer(storageChan chan<- Product, count int) {
	for {
		producer := Product{"商品：" + strconv.Itoa(count)}
		storageChan <- producer
		time.Sleep(time.Second)
		count--
		fmt.Println("生产了，", producer)
		if count < 1 {
			return
		}
	}
}

// storageChan 是只读。
func Logistics(storageChan <-chan Product, shopChan chan<- Product) {
	for {
		product := <-storageChan
		shopChan <- product
		fmt.Println("运输了，", product)
	}
}

func Consumer(shopChan <-chan Product, count int, exitChan chan<- bool) {
	for {
		product := <-shopChan
		fmt.Println("消费了，", product)
		count--
		if count < 1 {
			exitChan <- true
			return
		}
	}
}
```

## 管道定时任务应用

第一类：定时任务，类似延时消息队列。

第二类：周期性执行某个任务，类似定期同步某些数据。

```go
// 定时任务
package main

import (
	"fmt"
	"math/rand"
	"time"
)

var flag bool = isStopTimer()

func main() {
	fmt.Println("当前时间", time.Now())
	// 方式一
	timer := time.NewTicker(time.Second * 3)
	if flag {
		timer.Stop()
	} else {
		t := <-timer.C
		fmt.Println(t)
	}

	// 方式二
	// t := <-time.After(time.Second * 3)

}

func isStopTimer() bool {
	rand.Seed(time.Now().UnixNano())
	tempInt := rand.Intn(2) + 18
	if tempInt >= 18 {
		fmt.Println("已经找到了大于18，结束。")
		return true
	} else {
		return false
	}
}
```

```go
// 周期任务
package main

import (
	"fmt"
	"time"
)

func main() {
	var count int = 0
	ticker := time.NewTicker(time.Second * 1) // 定时器，定时间间隔。
	// 协程调用一个匿名函数，不停的输出定时器 管道中的时间，在>3时停下计时器。
	go func() {
		for {
			t := <-ticker.C
			fmt.Println("时间:", t.Format("2003-01-02 03:04:05PM"))
			count++
			if count > 3 {
				ticker.Stop()
			}
		}
	}()

	// for {
	// 	t := <-ticker.C
	// 	fmt.Println("时间:", t.Format("2003-01-02 03:04:05PM"))
	// 	count++
	// 	if count > 3 {
	// 		ticker.Stop()
	// 	}
	// }
	//在第一个示例中，ticker.Stop() 是在协程内部执行的，当 count 大于 3 时，
	// 它会停止计时器。这样，
	// 协程内的 for 循环会因为 ticker.C 的通道关闭而结束，
	// 从而协程退出，程序继续执行后续代码，不会发生死锁。
	// 而在第二个示例中，ticker.Stop() 是在主函数内部执行的，
	// 但 for 循环仍然在继续从 ticker.C 接收时间值。
	// 当 ticker.Stop() 被调用后，ticker.C 通道会被关闭，
	// 但由于 for 循环仍在尝试从已关闭的通道中接收数据，会导致死锁错误。

	//
	time.Sleep(time.Second * 10)
	fmt.Println("游戏结束")
}
```

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	var count int = 0
	var waitGroup sync.WaitGroup // 有点像 别的语言的进程池的意思。但是控制的协程。
	// 用于向 WaitGroup 中添加指定数量的等待任务。
	// 通常在启动 goroutine 之前调用，表示有多少个任务需要等待完成。
	waitGroup.Add(1)
	ticker := time.NewTicker(time.Second * 1) // 定时器，定时间间隔。
	// 协程调用一个匿名函数，不停的输出定时器 管道中的时间，在>3时停下计时器。
	go func() {
		defer ticker.Stop()    // 关闭定时器 的管道。
		defer waitGroup.Done() // 书写的好习惯。表示一个协程任务已经完成。
		for {
			t := <-ticker.C
			fmt.Println("时间:", t.Format("2003-01-02 03:04:05PM"))
			count++
			if count > 3 {
				return // 此时退出方法，进入defer的语句运行。
			}
		}
	}()

	//
	time.Sleep(time.Second * 5)
	waitGroup.Wait() // 结束协程池
	fmt.Println("游戏结束")
}
```

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	var count int = 0
	var waitGroup sync.WaitGroup // 有点像 别的语言的进程池的意思。但是控制的协程。
	// 用于向 WaitGroup 中添加指定数量的等待任务。
	// 通常在启动 goroutine 之前调用，表示有多少个任务需要等待完成。
	waitGroup.Add(1)
	ticker := time.NewTicker(time.Second * 1) // 定时器，定时间间隔。
	// 协程调用一个匿名函数，不停的输出定时器 管道中的时间，在>3时停下计时器。
	go func() {
		defer ticker.Stop()    // 关闭定时器 的管道。
		defer waitGroup.Done() // 书写的好习惯。表示一个协程任务已经完成。
		for {
			t := <-ticker.C
			fmt.Println("ticker 时间:", t.Format("2003-01-02 03:04:05PM"))
			count++
			if count > 2 {
				return // 此时退出方法，进入defer的语句运行。
			}
		}
	}()

	var countA int = 0
	timer := time.NewTimer(time.Second * 1) //单次触发的定时器，即定时器触发一次后就停止。
	// 协程调用一个匿名函数，不停的输出定时器 管道中的时间，在>3时停下计时器。
	go func() {
		defer timer.Stop()     // 关闭定时器 的管道。
		defer waitGroup.Done() // 书写的好习惯。表示一个协程任务已经完成。
		for {
			t := <-ticker.C
			fmt.Println("timer 时间:", t.Format("2003-01-02 03:04:05PM"))
			// 重新设置定时器 timer，使其在1秒后触发。这意味着原来的定时器计时将被取消，
			// 并在调用 Reset 后重新开始计时，1秒后触发。
			timer.Reset(time.Second)
			countA++
			if countA > 3 {
				return // 此时退出方法，进入defer的语句运行。
			}
		}
	}()
	//
	time.Sleep(time.Second * 5)
	waitGroup.Wait() // 结束协程池
	fmt.Println("游戏结束")
}
```

