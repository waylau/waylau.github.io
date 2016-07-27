---
layout: post
title:  Go For Java Programmers(面向Java开发者的GO编程)
date: 2013-01-13 00:30
author: admin
comments: true
categories: [Golang,Java]
tags: [Golang,Java]
---


本文旨在帮助JAVA开发者迅速掌握 Go语言.

开始用一个很容易能被所有的Java程序员认出的例子来突出特色，然后对GO的框架给出了详细的的描述，最后用一个例子来说明GO结构中没有与 Java直接对应处。

##Hello stack (一个栈的例子)
为了吊起你的胃口，我们用一个麻雀虽小，五脏俱全和符合习惯的例子对应这个[Stack.java](http://www.nada.kth.se/~snilsson/go_for_java_programmers/src/collection/Stack.java) 程序

```java

// 包collection实现了生成栈.
package collection

// 零值栈是一个空栈，准备被使用.
type Stack struct {
    data []interface{}
}

// Push函数将x添加到栈顶.
func (s *Stack) Push(x interface{}) {
    s.data = append(s.data, x)
}

// Pop函数是将栈顶元素移除并返回.
// 在Pop函数执行在空栈时，会被一个运行时的error警示.
func (s *Stack) Pop() interface{} {
    i := len(s.data) - 1
    res := s.data[i]
    s.data[i] = nil  // 避免内存泄露
    s.data = s.data[:i]
    return res
}

// Size函数返回栈中元素的个数
func (s *Stack) Size() int {
    return len(s.data)
}

```

[stack.go](http://www.nada.kth.se/~snilsson/go_for_java_programmers/src/collection/stack.go)

* 顶级声明出现之前，直接的评论是文档注释。他们是纯文字。.
* 对于声明，你把名字写在类型后面.
* `struct` 对应Java中的类, 但struct组成不是方法而只能是变量.
* `Tinterface{}`类型对应Java的 `Object`. 在GO中它被所有的类型所实现，而不仅仅是引用类型.
* 代码段 `(s *Stack)` 声明了一个方法，接收者 `s` 对应Java中的 `this`.
* 操作符`:=`声明并初始化了一个变量. 它的类型可以从初始化表达式中推导出.

这里是一个的Hello world程序，演示了如何使用`collection.Stack`的抽象数据类型.

```go

package collection_test

import (
    collection "."
    "fmt"
)

func Example() {
    var s collection.Stack
    s.Push("world")
    s.Push("hello, ")
    for s.Size() > 0 {
        fmt.Print(s.Pop())
    }
    fmt.Println()
    //输出: hello, world
}
```
[example_test.go](http://www.nada.kth.se/~snilsson/go_for_java_programmers/src/collection/example_test.go)

##概念上的差异

* Go的构造器没有类。Go 用 structs 和 interfaces来替代实例化方法，类的继承机制，动态方法查找.也可用于Java使用泛型接口
* Go提供所有类型的指针的值，而不只是对象和数组。对于任何类型 T，有一个相应的指针类型*T表示指针指向类型 T的值。 offers pointers to values of all types, not just objects and arrays.
* Go允许任何类型都有方法而没有装箱的限制 allows methods on any type; no boxing is required. 方法receiver,在Java中对应this可以是直接值或者是指针.
* 数组在Go就是值. 当一个数组被当做函数的参数时，这个函数接收到的是数组的拷贝而不是它的指针. 然而在实践中,函数经常使用 slices作为参数; slices引用了基础数组.
* 该语言提供了字符串，一个字符串行为就像一个字节片，但是是不可改变的。
* 该语言中的哈希表被称作maps.
* 该语言提供了独立运行的线程goroutines 和他们之间的通信渠道channels.
* 某些类型(maps, slices, 和 channels)是按引用传递，而不是值。也就是说，传递一个map到函数并而不是拷贝map，如果函数修改了map，将被调用者看到变化。在Java术语来说，可以认为这是一个map的引用.
* Go提供了两种访问级别对应Java的public和包的private.如果它的命名是大写字母开头就是最高级别public，反之就是包的private.
* 作为替换Java中的异常机制, Go采用了类型 error值来表示事件，如文件结尾,和运行时的panics来表示运行时的错误，如数组越界等.
* Go不支持隐式类型转换。混合使用不同类型的操作需要显式转换.
* Go不支持函数重载。在同一范围内的函数和方法必须具有唯一的名称.
* Go使用nil表示无效的指针，类似于Java使用null.

##句法
###声明

声明是跟Java是相反的。你在类型后面再写名称，类型声明从左往右更容易读

Go	与Java相对应的
<table>
<tbody>
<tr>
<th>Go</th>
<th>与Java相对应的</th>
</tr>
<tr>
<td><code>var v1 int</code></td>
<td><code>int v1;</code></td>
</tr>
<tr>
<td><code>var v2 *int</code></td>
<td><code>Integer v2;</code></td>
</tr>
<tr>
<td><code>var v3 string</code></td>
<td><code>String v3 = "";</code></td>
</tr>
<tr>
<td><code>var v4 [10]int</code></td>
<td><code>int[] v4 = new int[10]; // v4 在Go中是一个值.</code></td>
</tr>
<tr>
<td><code>var v5 []int</code></td>
<td><code>int[] v5;</code></td>
</tr>
<tr>
<td><code>var v6 *struct { a int } </code></td>
<td><code>C v6; // Given: class C { int a; }</code></td>
</tr>
<tr>
<td><code>var v7 map[string]int</code></td>
<td><code>HashMap&lt;String,Integer&gt; v7;</code></td>
</tr>
<tr>
<td><code>var v8 func(a int) int</code></td>
<td><code>F v8; // Given: interface F { int f(int a); }</code></td>
</tr>
</tbody>
</table>

声明的一般形式是一个关键字后面跟着被声明对象的名字.这个关键字是const, type, var, 或者func. 您也可以使用一个关键字，后面的括号中跟着一系列声明.

	var (
	    n int
	    x float64
	)

当声明一个函数，你必须提供每个参数的名称，或者不提供任何参数的名称，你不能提供了一些而忽略了另外一些名字。您可以组合几个相同类型的名称:
	
	func f(i, j, k int, s, t string)

一个变量可以在声明时初始化。当这样做时，指定的变量的类型是允许的，但不是必需的。当未指定类型，默认的是初始化表达式的类型.

	var v9 = *v2
如果一个变量没有立即初始化，必须要制定类型。那样的情况下，它它会被隐式初始化该类型的零值zero value(0, nil, "", 等.). Go不存在未初始化的变量.

###短声明

在函数中，一个短的声明句法是 := 表示.

	v10 := v1
这等效于

	var v10 = v1

###函数类型

在Go中，函数都是一等公民。Go的函数类型表示一组所有具有相同的参数和返回类型的函数.

	type binOp func(int, int) int
	
	var op binOp
	add := func(i, j int) int { return i + j }
	
	op = add
	n = op(100, 200)  // n = 100 + 200

###多重分配

GO允许多重分配。在右边的表达式会在评估后，再分配到任何的左操作数。

	i, j = j, i  //交换i和j.
函数可以具有多个返回值，表示由括号中的列表。返回的值可以存储分配给一个变??量列表。

	func f() (i int, pj *int) { ... }
	v1, v2 = f()

###空白标识符

空白标识符提供了一种忽略多值表达式返回值的方式，用下划线字符表示： The blank identifier, represented by the underscore character, provides a way to ignore values returned by a multi-valued expression:

	v1, _ = f()  // 忽略f()返回的第二个值.

###分号和格式

为了消除对分号和格式不必要的担忧，你可能会用gofmt程序来写GO风格的标准代码，虽然这种风格看起来很古怪，但熟悉了之后最终会像其他语言风格一样变得舒服

Go的代码在实际中很多出现分号。严格来说，Go所有的声明都用分号结束。但是Go毫无疑问会在每个非空白行的结尾插入一个分号，除非它还没有完. 这样做的后果是，在某些情况下，Go不允许断行。举例，你可能会像下面这样写：

	func g()
	{            //无效的;“{”应该是在前面那一行。
	}
在g()后面会被插入一个分号，这样就使他像是一个函数声明而不是函数定义了类似的，你不能这样写：

	if n == 0 {
	}
	else {       // 无效的; "else {" 应该是在前面那一行。
	}
在} 后和else前面会插入一个分号,导致句法错误.

###条件语句

Go在条件语句中并不使用括号，像 if条件语句 , for 条件语句的表达式, switch 条件语句的值. 另一方面，它并不需要在 if或 for条件语句中加花括号

	if a < b { f() }
	if (a < b) { f() }           // 括号是不必要的.
	if (a < b) f()               // 无效的
	for i = 0; i < 10; i++ {}
	for (i = 0; i < 10; i++) {}  // 无效的

此外, if和switch 接收一个可选的初始化的状态，那么惯用做法是建一个局部变量

	if err := file.Chmod(0664); err != nil {
	    log.Print(err)
	    return err
	}

###For 语句

Go没有while和do-while语句. 当for语句的条件比较单一时，他的作用就像是while语句. 完全省略条件则产生一个死循环。

for语句可能包含range遍历 strings, arrays, slices, maps, 或 channels。除了写成下面这样

	for i := 0; i < len(a); i++ { ... }
去遍历a的元素，也可以写成

	for i, v := range a { ... }
这里的i指索引， v代表的array, slice, 或者string的连续元素。对于字符串，i是一个字节的索引，v指向rune类型（rune类型是int32)的一个别名）。maps迭代产生键 - 值对，而channels只产生一个迭代值。

###Break和continue

像Java一样，GO许可break和continue指定一个标签，但标签上必须指的for, switch, 或者 select 语句.

###Switch 语句

在 switch 语句，case 标签默认情况下不通过，但你可以让他们 fallthrough语句结束的情况下通过了。

	switch n {
	case 0:  // empty case body
	case 1:
	    f()  // f is not called when n == 0.
	}
但是一个case可以包含过个值

	switch n {
	case 0, 1:
	    f()  // f is called if n == 0 || n == 1.
	}
case的值可以支持任何类型的相等比较操作符，如字符串或指针。一个丢失表达式的switch语句 等价于表达式为 true。

	switch {
	case n < 0:
	    f1()
	case n == 0:
	    f2()
	default:
	    f3()
	}
### ++ 和 -- 语句

++和--只能作为后缀操作符，和仅在语句中，而不是在表达式中。例如，你不可以写n = i++。

### defer语句

defer语句调用一个函数的执行被推迟到函数返回那一刻。defer语句执行时，被递延的函数的参数被计算，并保存，以备将来使用

	f, err := os.Open("filename")
	defer f.Close()  // f will be closed when this function returns.

##Constants（常量）
GO中的常数可能是untyped的。这适用于无类型的常量表达式的数字文本，和使用const声明的无类型的常量表达式。当它被用在需要一个带类型的值的背景下，一个无类型的常量的可以被转变成有类型的值。这样常量的使用相对自由，即使Go没有隐式类型转换

	var a uint
	f(a + 1)    // The untyped numeric constant 1 becomes typed as uint.
	f(a + 1e3)  // 1e3 is also typed as uint.
语言对非类型化的数字常量不限制大小。限制仅适用于使用一个常量时，其中一种类型是必需的。

	const huge = 1 << 100
	var n int = huge >> 98
如果是不存在的变量声明的类型和相应的表达式的计算结果为一个非类型化的数字常数，这个常数是被转换为 rune, int, float64, 或者complex128 类型，取决于该值是否一个字符，整数，浮点，或复杂的常数。

	c := '?'      // rune (alias for int32)
	n := 1 + 2    // int
	x := 2.7      // float64
	z := 1 + 2i   // complex128
GO 不存在枚举类型。相反，你可以使用特殊的名字iota在单一的const声明中从而得到一系列累加值。当初始化表达式省略为一个 const，它重用了前面的表达式。

	const (
	    red = iota  // red == 0
	    blue        // blue == 1
	    green       // green == 2
	)

## Structs（结构体）

结构体对应于Java中的类，但一个结构的成员不能是方法，而是变量。结构体的指针是类似Java的的引用变量。与Java类不同的是，结构也可以被定义为直接值。在这两种情况下使用 .来访问结构体的成员。

	type MyStruct struct {
	    s string
	    n int64
	}
	
	var x MyStruct      // x is initialized to MyStruct{"", 0}.
	var px *MyStruct    // px is initialized to nil.
	px = new(MyStruct)  // px points to the new struct MyStruct{"", 0}.
	
	x.s = "Foo"
	px.s = "Bar"
在Go中，方法可以与任何命名的类型关联，而不仅仅是与结构体。详情见方法和接口的讨论。

##Pointers（指针）

如果你有一个int或struct或者array需要分配对象的内容复制。 想达到Java的引用变量的效果，Go使用指针。对于任何类型e T,有一个相应的指针类型*T，表示指针类型 T的值

给指针变量分配存储空间，使用内置函数new，传入一个类型，并返回一个指针，指向分配的存储空间。分配的空间将被零初始化的类型。例如， new(int) 分配存储为一个新的int，初始化它的值为e 0，并返回它的地址，类型 *int。

Java代码T p = new T()，其中 T是一个两个int型实例变量a和b的类，对应于

	type T struct { a, b int }
	var p *T = new(T)
或者习惯性这样干

	p := new(T)
var v T代表声明，声明了一个变量包含一个值类型 T，这在Java中是没有的。也可使用复合方式创建并初始化值。

	v := T{1, 2}
等同于

	var v T
	v.a = 1
	v.b = 2
对于类型T的操作数x，地址运算符 &x提供值类型为*T的x的地址，

##Slices（切片）
slice是概念上一个结构包含三个域：一个数组的指针、长度和容量。切片支持[]操作符来访问底层数组的元素。内置的len函数返回的切片长度。内置的的cap函数返回切片的能力。

给定一个数组，或另一个切片，通过a[i:j]来创建一个新的切片。这个新创建的切片指向a，从索引i开始，并结束索引j之前。它的长度是j - i。如果i 被省略，切片从0开始。如果j 被省略，切片在 len(a)结束。新切片跟 a一样指向相同的数组。即，改变后组成的新的切片的元素在a都能见到。新切片的容量就是简单的a减去i。数组的容量就是数组的长度。

	var s []int
	var a [10]int

	s = a[:]  // short for s = a[0:len(a)]
如果你创建一个值类型为[100]byte（100个字节，也许是一个缓冲区的数组），你想不复制它，而将它传递给函数，那么函数的参数声明类型[]byte，并传入数组的切片。切片也可以用 make的函数创建（如下文所述）。

切片组合采用内置的append函数，Java的ArrayList提供相同的功能。

	s0 := []int{1, 2}
	s1 := append(s0, 3)      // append a single element
	s2 := append(s1, 4, 5)   // append multiple elements
	s3 := append(s2, s0...)  // append a slice
切片语法，也可以使用在字符串上。它返回一个新字符串，其值是原始的字符串的子串

##make函数
Map and channel values must be allocated using the built-in function make. For example, calling map和channel值必须使用内置的函数make。例如，调用

make(map[string]int)
map[string]int返回一个新分配的值类型。相对于new， make 返回的是实际的对象，而不是一个地址。这是一致的事实，map和channel是引用类型。

对于map，make函数将容量作为一个可选的第二个参数的提示。对于channel，有一个可选的第二个参数来设置channel的缓冲能力，默认为0（无缓冲）。

make函数也可以用来分配一个切片。在这种情况下，它分配内存给基本数组并返回一个引用他的切片。该切片中的元素数是一个必需的参数。第二个可选的参数是切片的容量。

	m := make([]int, 10, 20)  // Same as new([20]int)[:10]

##方法和接口
###方法

方法看起来像一个普通的函数定义，但它有一个receiver（接收者）。receiver是类似Java实例方法中的this引用。

	type MyType struct { i int }
	
	func (p *MyType) Get() int {
	    return p.i
	}
	
	var pm = new(MyType)
	var n = pm.Get()
这声明了一个方法Get与MyType关联的。receiver被命名为p 在函数体内。

命名的类型来定义方法。如果您转换不同类型的值，新的值将有新的类型，而不是那些旧的类型。

你可以定义一个内置类型的方法，用新的命名类型声明。新的类型和内置的类型是不同的。

	type MyInt int
	
	func (p MyInt) Get() int {
	    return int(p)  // The conversion is required.
	}
	
	func f(i int) {}
	var v MyInt
	
	v = v * v          // The operators of the underlying type still apply.
	f(int(v))          // int(v) has no defined methods.
	f(v)               // INVALID

###接口

Go接口类似于Java接口，但可被视为一个实现该接口提供任何类型的在Go接口命名的方法。明确的声明是不必要的。

接口像这样:

	type MyInterface interface {
	    Get() int
	    Set(i int)
	}
自从 MyType 已经有了Get 方法, 我们可以让 MyType满足接口通过添加

	func (p *MyType) Set(i int) {
	    p.i = i
	}
现在任何只要将MyInterface当做参数就可以接收类型是*MyType的变量

	func GetAndSet(x MyInterface) {}
	
	func f1() {
	    var p MyType
	    GetAndSet(&p)
	}
在Java术语，给MyType 定义 Set和Get 使MyType自动实现了MyInterface接口。这种类型型可满足多个接口。这是一种形式的鸭子类型。

>“当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子。”
James Whitcomb Riley

###匿名域

匿名域可以用于实现很像一个Java子类的东西。

	type MySubType struct {
	    MyType
	    j int
	}
	
	func (p *MySubType) Get() int {
	    p.j++
	    return p.MyType.Get()
	}
	MySubType有效实现的像是MyType的子类型.
	
	func f2() {
	    var p MySubType
	    GetAndSet(&p)
	}
Set方法是继承自MyType的，因为关联了匿名域的方法的变为了封闭类型的方法。在这种情况下，因为 MySubType有一个匿名与域 MyType类型，所以为 MyTypee的方法也成为MySubType的方法。Get方法被重写，Set方法被继承。

这是与Java中的子类不完全相同。当一个匿名域的方法被调用时，它的 receiver就是这个匿名域，而不是周围的结构体。换句话说，匿名域上的方法的不会动态调度。当你想要实现相当于Java的动态方法查找，请使用接口。

	func f3() {
	    var v MyInterface
	
	    v = new(MyType)
	    v.Get()  // Call the Get method for *MyType.
	
	    v = new(MySubType)
	    v.Get()  // Call the Get method for *MySubType.
	}

###类型断言

使用一个类型断言可以使具有一个接口类型的变量转换成具有不同的接口类型。这是在运行时动态执行。与Java不同，并不需要任何声明两个接口之间的关系。

	type Printer interface {
	    Print()
	}
	
	func f4(x MyInterface) {
	    x.(Printer).Print()  // type assertion to Printer
	}
转换为Printer 完全是动态的。只要x（x中存储的值的实际类型）的 动态类型 定义了一个Print方法。

##Errors(错误机制)
Go的多值的返回可以很容易地沿着正常的返回值返回详细的错误消息。按照惯例，这样的error类型的消息，就像下面的简单的内置接口

	type error interface {
	    Error() string
	}
例如，os.Open函数当它无法打开文件时返回一个非零error值。

	func Open(name string) (file *File, err error)
以下代码使用 os.Open打开一个文件。如果 error发生，它调用 log.Fatal打印错误信息并停止。
	
	f, err := os.Open("filename.ext")
	if err != nil {
	    log.Fatal(err)
	}
	// do something with the open *File f
error的接口只需要一个 Error的方法，但具体的error的实现往往有额外的方法，允许调用者进行检查错误的详细信息

##Panic and recover（恐惧和恢复）
panic（恐慌）是一个运行时错误，展开goroutine的堆栈，一路运行任何递延的功能，然后停止程序。恐慌与Java异常相似，但只适用于运行时的错误，如一个nil 指针或试图索引数组越界。 Go程序使用内置的error类型 （详见上文）为了表示如文件结束等事件。

可以使用内置的recover （恢复），重新获得控制权的恐慌的goroutine和恢复正常运行。呼叫recover停止展开，并返回传入的参数panic。因为只有运行的未展开代码，内部含有延迟函数，recover只在内递延的函数有效。如果的goroutine是没有恐慌，recover返回nil。

##Go的线程机制和管道
###Goroutines（Go的线程机制）

Go允许用go开启一个新的执行线程--goroutine。它运行在不同的，新创建的的goroutine中。在一个程序中的所有goroutine共享相同的地址空间。

Goroutines是轻量级的，只占用比堆栈分配多一点的空间。堆栈开始小和成长的分配和释放堆（heap）的要求。内部goroutines像进行了复用多个操作系统线程的协程。您不必担心这些细节。

	go list.Sort()  // Run list.Sort in parallel; don’t wait for it.
Go处理文字的函数，可以作为结束，在处理go时很强大

	func Publish(text string, delay time.Duration) {
	    go func() {
	        time.Sleep(delay)
	        fmt.Println(text)
	    }()  // Note the parentheses. We must call the function.
	}
变量text和delay在周围函数和函数文字之间共享;只要它们都可以访问，它们就存在。

###Channels（管道）

管道通过指定的元素类型的值来提供两个goroutine同步执行和沟通的机制。 <- 操作符指定通道的方向，发送或接收。如无任何指示方向时，通道是双向的。

	chan T          // can be used to send and receive values of type T
	chan<- float64  // can only be used to send float64s
	<-chan int      // can only be used to receive ints
管道是一个引用类型，用make分配。

	ic := make(chan int)        // unbuffered channel of ints
	wc := make(chan *Work, 10)  // buffered channel of pointers to Work

To receive a value on a channel, use it as a unary operator. 使用<- 作为一个二元操作符来在管道上发送值。当在管道上接收一个值时，把它作为一元运算符。

	ic <- 3       // Send 3 on the channel.
	work := <-wc  // Receive a pointer to Work from the channel.
如果管道是无缓冲，那么发送者阻塞，直到接收器接收到值。如果管道有一个缓冲区，发送者阻塞，直到该值已被复制到缓冲区。如果缓冲区已满，这意味着等待，直到一些接收器中检索到值。接收器被阻塞，直到有数据接收。

##并发 (示例)
最后我们用一个例子来说明如何散落的内容拼起来。这是一个服务器通过管道来接受的Work请求的例子。每个请求都在一个单独的goroutine运行。 Work 结构本身包含了一个管道，用于返回一个结果。
	
	package server
	
	import "log"
	
	// New creates a new server that accepts Work requests
	// through the req channel.
	func New() (req chan<- *Work) {
	    wc := make(chan *Work)
	    go serve(wc)
	    return wc
	}
	
	type Work struct {
	    Op    func(int, int) int
	    A, B  int
	    Reply chan int  // Server sends result on this channel.
	}
	
	func serve(wc <-chan *Work) {
	    for w := range wc {
	        go safelyDo(w)
	    }
	}
	
	func safelyDo(w *Work) {
	    // Regain control of panicking goroutine to avoid
	    // killing the other executing goroutines.
	    defer func() {
	        if err := recover(); err != nil {
	            log.Println("work failed:", err)
	        }
	    }()
	    do(w)
	}
	
	func do(w *Work) {
	    w.Reply <- w.Op(w.A, w.B)
	}
[server.go](http://www.nada.kth.se/~snilsson/go_for_java_programmers/src/server/server.go)
下面展示如何使用：

	package server_test
	
	import (
	    server "."
	    "fmt"
	)
	
	func main() {
	    s := server.New()
	
	    divideByZero := &server.Work{
	        Op:    func(a, b int) int { return a / b },
	        A:     100,
	        B:     0,
	        Reply: make(chan int),
	    }
	    s <- divideByZero
	
	    add := &server.Work{
	        Op:    func(a, b int) int { return a + b },
	        A:     100,
	        B:     200,
	        Reply: make(chan int),
	    }
	    s <- add
	
	    fmt.Println(<-add.Reply)
	    // Output: 300
	}
[example_test.go](http://www.nada.kth.se/~snilsson/go_for_java_programmers/src/server/example_test.go)
并发编程是一个大主题，Java和Go的方法是完全不同的。要想充分体验到并发编程的乐趣，看这篇[Share Memory by Communicating（《通过沟通共享内存》）](http://golang.org/doc/codewalk/sharemem/)

Stefan Nilsson

该文基于相似的文章 [《面向C++的GO编程》](http://code.google.com/p/go-wiki/wiki/GoForCPPProgrammers)

参考： <http://www.nada.kth.se/~snilsson/goforjava_programmers>