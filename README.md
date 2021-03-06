# go-study

> 前端程序员食用的`go`语言学习笔记

## GO语言特性

> 静态、编译型语言，具备垃圾回收功能、支持并发、匿名函数、延迟触发和函数多返回值。

和`JavaScript`让人不习惯的地方：

- 不需要使用分号
- `if`后可不用`()`包裹判断条件
- 没有`while`，只有`for`循环
- 没有隐式类型转换
- 变量声明后会自带默认值，不会是`undefined`
- 可以将初始化和判断写在一起
- 支持使用复数

## 常用命令

```shell
go run main.go # >>> 执行go文件
go build main.go # >>> 生成可执行的二进制文件
go get {url} # >>> 获取网上的代码
```



## 基础语法

## 变量类型

### 值类型

> 基本数据类型是Go语言实际的原子，复合数据类型是由不同的方式组合基本类型构造出来的数据类型

- `string`、`bool`

- `int` >>> 0

- `int8`,`int16`,`int32`,`int64` >>> 分别对应8、16、32、64`bit`大小的有符号整数

- `uint8`,`uint16`,`uint32`,`uint64` >>> 分别对应8、16、32、64`bit`大小的无符号整数

- `byte` >>> 字符型(8`bit`)

- `rune`  >>> 字符型(32`bit`)

- `float32`,`float64`

  - 一个`float32`类型的浮点数可以提供大约6个十进制数的精度，而`float64`则可以提供约15个十进制数的精度；通常应该优先使用`float64`类型，因为`float32`类型的累计计算误差很容易扩散，并且`float32`能精确表示的正整数并不是很大

- `complex64`,`complex128`

  - > 我们把形如 z=a+bi（a、b均为实数）的数称为复数。
    > 其中，a 称为实部，b 称为虚部，i 称为虚数单位。
    > 当 z 的虚部 b＝0 时，则 z 为实数；当 z 的虚部 b≠0 时，实部 a＝0 时，常称 z 为纯虚数。
    > 复数域是实数域的代数闭包，即任何复系数多项式在复数域中总有根。

  - 分别对应`float32`和`float64`两种浮点数精度。

  - 复数也可以用==和!=进行相等比较。只有两个复数的实部和虚部都相等的时候它们才是相等的

  - 内置的complex函数用于构建复数，内建的`real`和`imag`函数分别返回复数的实部和虚部：

    ```go
    var x complex128 = complex(1, 2) // 1+2i
    var y complex128 = complex(3, 4) // 3+4i
    fmt.Println(x*y)                 // "(-5+10i)"
    fmt.Println(real(x*y))           // "-5"
    fmt.Println(imag(x*y))           // "10"
    ```

### 数值类型

> 整数、浮点数、复数

**`go`中也存在`NaN`非数**

```go
var z float64
// 输出 "0 -0 +Inf -Inf NaN"
fmt.Println(z, -z, 1/z, -1/z, z/z) 
```

- 函数`math.IsNaN`用于测试一个数是否是非数NaN，
- 函数`math.NaN`则返回非数对应的值。
- 虽然可以用math.NaN来表示一个非法的结果，但是测试一个结果是否是非数NaN则是充满风险的，因为NaN和任何数都是不相等的。

### 字符串

#### 字符

Golang 中没有专门的字符类型，如果要存储单个字符(字母)，一般使用 byte 来保存，且使用单引号包裹。

#### 字符串

字符串在Go语言中是基本类型，内容在初始化后不能修改。

Go中的字符串都是采用UTF-8字符集编码，使用一对双引号`""`或反引号````定义。````可以额外解析换行，即其没有字符转义功能。

##### 修改GO中的字符串

1. 转换为字节数组`[]byte`类型，构建新的临时字符串

   ```go
   str := "hello"
   temp := []byte(str)
   temp[0] = 'H'
   res := string(temp)
   ```

2. 使用切片，修改原字符串

   ```go
   str := "hello"
   str = "H" + str[1:]
   ```

##### 常用操作

1. `len()`，字符串长度
2. `string()`，字符串类型转换

### 数组

#### 数组声明

```go
var arr [10]int{1,2,3,8:10}
var arr [...]int{1,2,5:11}
```

#### 数组常用操作（切片转化）

```go
arr[:] // 所有元素
arr[:5] //前5
arr[5:] //后5
```

**数组之间的赋值是值的赋值，即当把一个数组作为参数传入函数的时候，传入的其实是该函数的副本，而不是他的指针。**

### 结构体

```go
type Student struct {
	id int
	age int 
	name string
}

func main() {
    stu := new(Student) //被new生成的结构体实例其实是指针类型
    stu1 := &Student{}  //对结构体进行&取地址操作时，也可以视为对该类型进行一次new的实例化操作。
}
```

- struct的结构中的类型可以是任意类型，且存储空间是连续的，其字段按照声明时的顺序存放
- 如果结构体的所有的成员都是可以比较的，那么结构体本身也是可以比较的，使用 == != ，不支持 > 和 <

### 零值机制

> `go`语言变量初始化会自带默认值

```
int     0
int8    0
int32   0
int64   0
uint    0x0
rune    0           //rune的实际类型是 int32
byte    0x0         // byte的实际类型是 uint8
float32 0           //长度为 4 byte
float64 0           //长度为 8 byte
bool    false
string  ""
```

### 引用类型

> 保存的是对程序中一个变量的或状态的间接引用，对其修改将影响所有该引用的拷贝

- `uintptr`指针 >>> *
- 切片 >>> slice
- 字典 >>> map
- 函数 >>> func
- 管道 >>> chan
- 接口 >>> interface

### 切片

#### 创建

```go
s2 := []byte{'a','b','c'}

// make
slice1 := make([]int, 6, 10)// 创建长度为5，容量为10，初始值为0的切片
```

slice可以从一个数组再次声明。slice通过array[i:j]来获取，其中i是数组的开始位置，j是结束位置，但不包含array[j]，它的长度是j-i:

- 组成：
  - 指针：指向第一个slice元素对应的底层数组元素的长度的地址（slice第一个元素不等于数组第一个元素）
  - 长度：slice元素的数量（`len()`获取slice长度）
  - 容量：长度不能超过容量，容量一般时从slice的开始位置到底层数据的结尾位置（`cap()`获取slice容量）
- 切片操作:**`s[i:j]`，其中0 ≤ i≤ j≤ cap(s)**
  - 新的slice将只有j-i个元素。
  - 如果i位置的索引被省略的话将使用0代替
  - 如果j位置的索引被省略的话将使用len(s)代替

#### 常用操作

切片常用内置函数：

```go
len()			返回切片长度
cap()			返回切片底层数组容量
append()		对切片追加元素
func copy(dst, src []Type) int
				将src中数据拷贝到dst中，返回拷贝的元素个数
```

> slice不支持比较运算符的原因
>
> - 第一个原因，一个slice的元素是间接引用的，一个slice甚至可以包含自身（当slice声明为[]interface{}时，slice的元素可以是自身）。虽然有很多办法处理这种情形，但是没有一个是简单有效的。 
>- 第二个原因，因为slice的元素是间接引用的，一个固定的slice值（指slice本身的值，不是元素的值）在不同的时刻可能包含不同的元素，因为底层数组的元素可能会被修改。而例如Go语言中map的key只做简单的浅拷贝，它要求key在整个生命周期内保持不变性（例如slice扩容，就会导致其本身的值/地址变化）。而用深度相等判断的话，显然在map的key这种场合不合适。对于像指针或chan之类的引用类型，==相等测试可以判断两个是否是引用相同的对象。一个针对slice的浅相等测试的==操作符可能是有一定用处的，也能临时解决map类型的key问题，但是slice和数组不同的相等测试行为会让人困惑。因此，安全的做法是直接禁止slice之间的比较操作。

### 集合

#### 创建

```go
m := map[string]int{"a": 1}
m1 := make(map[string]int)
```

- map是无序的，每次打印出来的map都会不一样，它不能通过index获取，而必须通过key获取,在实践中，遍历的顺序是随机的，每一次遍历的顺序都不相同。这是故意的，每次都使用随机的遍历顺序可以强制要求程序不会依赖具体的哈希函数实现。如果要按顺序遍历key/value对，我们必须显式地对key进行排序，可以使用sort包的Strings函数对字符串slice进行排序。
- map的长度是不固定的，也就是和slice一样，也是一种引用类型
- 内置的len函数同样适用于map，返回map拥有的key的数量
- go没有提供清空元素的方法，可以重新make一个新的map，不用担心垃圾回收的效率，因为go中并行垃圾回收效率比写一个清空函数高效很多
- map和其他基本型别不同，它不是thread-safe，在多个go-routine存取时，必须使用mutex lock机制

#### 并发的集合处理

> 因为用两个并发程序不断的对map进行读和写，产生了竞态问题。map内部会对这种错误进行检查并提前发现。
>
> Go内置的map只有读是线程安全的，读写是线程不安全的。

在`go1.9`版本中提供了更高效并发安全的`sync.Map`。

- Store表示存储
- Load表示获取
- Delete表示删除

```go
var scene sync.Map
scene.Store("name", "Tom")
scene.Range(func (k, v interface{}) bool {
	fmt.println(k, v)
	return true
})
```

`sync.Map`为了并发安全。损失了一定的性能。

### 指针

- 一个指针的值是另一个变量的地址。一个指针对应变量在内存中的存储位置。并不是每一个值都会有一个内存地址，但是对于每一个变量必然有对应的内存地址。
- 对于聚合类型每个成员——比如结构体的每个字段、或者是数组的每个元素——也都是对应一个变量，因此可以被取地址。 
- 任何类型的指针零值都是`nil`

```go
x := 1
p := &x
fmt.println(*p)
```

#### `new`函数

- 表达式`new(T)`将创建一个T类型的匿名变量，初始化为`T`类型的零值，然后**返回变量地址**，**返回的指针类型为`*T`**。
- new函数类似是一种语法糖，而不是一个新的基础概念
- 每次调用`new`函数都会返回一个新的变量地址，如果两个类型都是空的，也就是说类型的大小是0，例如`struct{}`和`[0]int`，有可能有相同的地址（依赖具体的语言实现）,请谨慎使用大小为0的类型，因为如果类型的大小为0的话，可能导致Go语言的自动垃圾回收器有不同的行为

### 变量声明

> `go`语言变量声明主要使用`var`，这里的`var`和`Javascript`中的`var`有相同的作用，都是用于定义一个可变的变量。

变脸声明的三种方式：

```go
var a int
var a, b int = 10
var a, b int = 10, 20
var a, _, c = 10, 20 ,30 // 丢弃20这个值
var (
	a int
    b string
)
var (
	a = 1
    b = 30
    c = "test"
)
c := 30 // := 这种方式只能在函数内部使用
```

`go`语言大小写敏感，并且推荐使用驼峰命名。

### 常量声明

`const`：定义常量

- 常量表达式的值在编译期计算，而不是在运行期。
- 每种常量的潜在类型都是基础类型：boolean、string或数字。
- `iota`(枚举)
  - 常量声明可以使用iota常量生成器初始化，它用于生成一组以相似规则初始化的常量，但是不用每行都写一遍初始化表达式
  - 在第一个声明的常量所在的行，iota将会被置为0，然后在每一个有常量声明的行加一。

```go
const (
	fileName = "txt"
    a,b = 34,4
)

const (
    a = iota    	        // 0
    b =	iota 		        // 1        
    c = iota 		        // 2
)

const (
    d = iota    	//  0
    e 				// 1        
    f 				// 2
)

//如果iota在同一行，则值都一样
const (
    g = iota    	        //0
    h,i,j = iota,iota,iota      // 1,1,1
    // k = 3                    // 此处不能定义缺省常量，会编译错误	
)
```

## 流程控制

### 条件语句

- `if……else……`

  ```go
  if err != nil {
  	panic(err)
  }
  
  if err := os.Readfile(filename);err != nil {
  	fmt.Println(err)
  }
  ```

  `go`语言支持将初始化和判断写在一起，并且利用分号(`;`)进行划分

- `switch` >>> 在每一个`case`语句后会自动`break`,除非使用`fallthrough`

  ```go
  switch num {
     case 1:                          // case 中可以是表达式
        fmt.Println("111")
     case 2:
        fmt.Println("222")
     default:
        fmt.Println("000")
  }
  
  
  switch {
  	case score < 60: g="F"
      case score < 90: g="A"
      default: panic(fmt.Sprintf("worng score"))
  }
  ```

### 循环语句

**go支持`for`**循环一种循环语句，并且省略括号。

```go
// 普通使用方式
a:=0
for i := 1; i< 100; i++ {
    a += i;
}

// for 循环简化
var i int
for ;;i++ {
    if i > 10 {
        break;
    }
}

// 模拟while 循环
var i = 100
for i < 0 {
    i--;
}

// 死循环
for {
    i ++;
}

for k, v := range []int{1,2,3} {
    fmt.Println(k,v)
}
```

#### 跳出循环：

- break
- continue
- `goto`：用于跳出多重循环

```go
for x:=0; x<10; x++ {
 
   for y:=0; y<10; x++ {

        if y==2 {
            goto breakHere
         }
   }
   
}
breakHere:
   fmt.Println("break")

// 统一错误处理
if err != nil {
   goto onExit
}
onExit:
   fmt.Pritln(err)
   exitProcess()
```

## 运算符

### 自增自减

`Go`中`++`、`--`不能用于表达式中，只能独立使用，且只存在后置用法。

### 优先级

![](https://github.com/baiziyu-personal-product/over-golang/raw/master/images/go/01-01.svg)

## 函数

- 函数的类型被称为函数的签名。
- Go语言没有默认参数值，也没有任何方法可以通过参数名指定形参，因此形参和返回值的变量名对于函数调用者而言没有意义。
- 引用类型实参：切片、map、function、channel等
- 在Go中，一个函数可以返回多个值。
- 如果一个函数所有的返回值都有显式的变量名，那么该函数的return语句可以省略操作数
- 通常，当函数返回non-nil的error时，其他的返回值是未定义的（undefined），这些未定义的返回值应该被忽略
- Go使用控制流机制（如if和return）处理错误
- 可变参数
  
  - 加上`...`
  
### Deferred函数

  - 你只需要在调用普通函数或方法前加上关键字defer，就完成了defer所需要的语法。当执行到该条语句时，函数和参数表达式得到计算，但直到包含该defer语句的函数执行完毕时，defer后的函数才会被执行，不论包含defer语句的函数是通过return正常结束，还是由于panic导致的异常结束。你可以在一个函数中执行多条defer语句，它们的执行顺序与声明顺序相反。

### 匿名函数

    - 当匿名函数需要被递归调用时，我们必须首先声明一个变量（在上面的例子中，我们首先声明了 visitAll），再将匿名函数赋值给这个变量。如果不分成两步，函数字面量无法与visitAll绑定，我们也无法递归调用该匿名函数。

- Panic异常

> 不加区分的恢复所有的panic异常，不是可取的做法；因为在panic之后，无法保证包级变量的状态仍然和我们预期一致。比如，对数据结构的一次重要更新没有被完整完成、文件或者网络连接没有被关闭、获得的锁没有被释放。此外，如果写日志时产生的panic被不加区分的恢复，可能会导致漏洞被忽略。
>
> - 在运行时检查，如数组访问越界、空指针引用等。这些运行时错误会引起painc异常。
> - 不是所有的panic异常都来自运行时，直接调用内置的panic函数也会引发panic异常
> - 一般而言，当panic异常发生时，程序会中断运行，并立即执行在该goroutine中被延迟的函数（defer 机制）。随后，程序崩溃并输出日志信息。日志信息包括panic value和函数调用的堆栈跟踪信息。panic value通常是某种错误信息。对于每个goroutine，日志信息中都会有与之相对的，发生panic时的函数调用堆栈跟踪信息。通常，我们不需要再次运行程序去定位问题，日志信息已经提供了足够的诊断依据。因此，在我们填写问题报告时，一般会将panic异常和日志信息一并记录。

### Recover异常捕获

- 如果在deferred函数中调用了内置函数recover，并且定义该defer语句的函数发生了panic异常，recover会使程序从panic中恢复，并返回panic value。
- deferred函数帮助Parse从panic中恢复。在deferred函数内部，panic value被附加到错误信息中；并用err变量接收错误信息，返回给调用者。我们也可以通过调用runtime.Stack往错误信息中添加完整的堆栈调用信息。

### `Init`函数

`init`函数是一个特殊的函数，它会在包完成初始化后自动执行，执行优先级高于`main`函数，并且不能手动调用`init`函数，每一个文件可以有多个`init`函数，初始化过程会根据包的以来关系顺序单线程执行。

```go
func CountWordsAndImages(url string) (words, images int, err error) {
    resp, err := http.Get(url)
    if err != nil {
        return
    }
    doc, err := html.Parse(resp.Body)
    resp.Body.Close()
    if err != nil {
        err = fmt.Errorf("parsing HTML: %s", err)
        return
    }
    words, images = countWordsAndImages(doc)
    return
}

//按照返回值列表的次序，返回所有的返回值，在上面的例子中，每一个return语句等价于：

return words, images, err

// 匿名函数

strings.Map(func (r rune) rune {return r +1}, "HAL-9000")

visitAll := func(items []string) {
// ...
visitAll(m[item]) // compile error: undefined: visitAll
// ...
}
```

## 方法

### 方法声明

> 在函数声明时，在其名字之前放上一个变量，即是一个方法。这个附加的参数会将该函数附加到这种类型上，即相当于为这种类型定义了一个独占的方法。

当调用一个函数时，会对其每一个参数值进行拷贝

## 接口

> 在Go语言中还存在着另外一种类型：接口类型。接口类型是一种抽象的类型

即使没有接口也能运行，但是当存在接口时，会隐式实现接口，让接口给类提供约束。

Go中不仅结构体之间可以嵌套，接口之间也可以嵌套。接口与接口嵌套形成了新的接口，只要接口的所有方法被实现，则这个接口中所有嵌套接口的方法均可以被调用。

```go
type Writer interface {
    // Write writes len(p) bytes from p to the underlying data stream.
    // It returns the number of bytes written from p (0 <= n <= len(p))
    // and any error encountered that caused the write to stop early.
    // Write must return a non-nil error if it returns n < len(p).
    // Write must not modify the slice data, even temporarily.
    //
    // Implementations must not retain p.
    Write(p []byte) (n int, err error)
}
```

### 空接口

空接口的类型和可比较性：

| 类型   | 说明                                               |
| ------ | -------------------------------------------------- |
| map    | 不可比较，会发生宕机错误                           |
| 切片   | 不可比较，会发生宕机错误                           |
| 通道   | 可比较，必须由同一个make生成，即同一个通道才是true |
| 数组   | 可比较，编译期即可知道是否一致                     |
| 结构体 | 可比较，可诸葛比较结构体的值                       |
| 函数   | 可比较                                             |

### 接口类型转换

#### 断言

在接口定义时，其类型已经确定，因为接口的本质是方法签名的集合，如果两个接口的方法签名结合相同（顺序可以不同），则这2个接口之间不需要强制类型转换就可以相互赋值，因为go编译器在校验接口是否能赋值时，比较的是二者的方法集。

接口类型无法直接访问其具体实现类的成员，需要使用断言（type assertions），对接口的类型进行判断，类型断言格式

```go
t, ok := i.(T)		// 安全写法：如果接口未实现接口，将会把ok掷为false，t掷为T类型的0值

```

# 并发编程

## Goroutines

> 每一个并发的执行单元叫做goroutine，这里可以将goroutine类看作一个线程

当程序启动时，主函数会在一个单独的goroutine中运行，我们称为`main goroutine`。

## channels 并发体之间的通信机制

Channel还支持close操作，用于关闭channel，随后对基于该channel的任何发送操作都将导致panic异常

一个channel有发送和接收两个机制，发送和接受都是用`<-`运算符。
- 发送语句中用来分割`channel`和要发送的值
- 接受语句中写在`channel`对象之前

以最简单方式调用make函数创建的是一个无缓存的channel，但是我们也可以指定第二个整型参数，对应channel的容量。如果channel的容量大于零，那么该channel就是带缓存的channel。

### 不带缓存的channels（同步channels）

- 一个基于无缓存Channels的发送操作将导致发送者goroutine阻塞，直到另一个goroutine在相同的Channels上执行接收操作，当发送的值通过Channels成功传输之后，两个goroutine可以继续执行后面的语句。反之，如果接收操作先发生，那么接收者goroutine也将阻塞，直到有另一个goroutine在相同的Channels上执行发送操作。
- 基于无缓存Channels的发送和接收操作将导致两个goroutine做一次同步操作。
- > 基于channels发送消息有两个重要方面。首先每个消息都有一个值，但是有时候通讯的事实和发生的时刻也同样重要。当我们更希望强调通讯发生的时刻时，我们将它称为消息事件。有些消息事件并不携带额外的信息，它仅仅是用作两个goroutine之间的同步，这时候我们可以用struct{}空结构体作为channels元素的类型，虽然也可以使用bool或int类型实现同样的功能，done <- 1语句也比done <- struct{}{}更短。

### 串联的channels(pipeline)
![](https://books.studygolang.com/gopl-zh/images/ch8-01.png) 

### 单方向的Channel

- 当一个channel作为一个函数参数时，它一般总是被专门用于只发送或者只接收。

### 带缓存的channels

- 带缓存的Channel内部持有一个元素队列。队列的最大容量是在调用make函数创建channel时通过第二个参数指定的。
- ![](https://books.studygolang.com/gopl-zh/images/ch8-02.png)
- 向缓存Channel的发送操作就是向内部缓存队列的尾部插入元素，接收操作则是从队列的头部删除元素。如果内部缓存队列是满的，那么发送操作将阻塞直到因另一个goroutine执行接收操作而释放了新的队列空间。相反，如果channel是空的，接收操作将阻塞直到有另一个goroutine执行发送操作而向队列插入元素。

#### 并发的循环


````go
ch := make(chan int)

ch <- x  //send
x = <- ch //receive
<-ch //receive statement

ch = make (chan int)
ch = make(chan int, 0)
ch = make(chan int, 3)
````

## Go语言的自动垃圾收集器

> 从每个包级的变量和每个当前运行函数的每一个局部变量开始，通过指针或引用的访问路径遍历，是否可以找到该变量。如果不存在这样的访问路径，那么说明该变量是不可达的，也就是说它是否存在并不会影响程序后续的计算结果。


## 实战

1. [gee-web](./gee-web)

## 框架

### Iris

1. Get `Iris`

- 使用命令行
```shell
# install iris v12
go get github.com/kataras/iris/v12@lastest
```
- 修改 `go.mod`

```go
module your_project_name

go 1.14

require (
    github.com/kataras/iris/v12 v12.1.8
)
```

2. 入门

- 创建新的服务
  - `iris.New()`：返回一个可配置的`iris.Application`实例
  - `app.Run`：第一个参数为需要启动的服务或者监听的Listener，第二个及之后的参数是可选的`iris.Configurator`配置参数。**在`iris`中每一个核心的模块（视图引擎、websocket、session等）都有一个内部的`iris.Configurator`**
  - `app.Listen`：传入需要监听的端口号
```go
app := iris.New()
// 这行代码会启动一个服务并监听 localhost:8080或者127.0.0.1:8080
app.Listen(":8080")


/*-------------------------------------*/

// 当然，想要获取完整的`http.Server`的实例的使用也是可以的
import "net/http"

// 这里实现的功能和上面的代码没有差异，但是可以确保你使用完整的`http.Server`实例
server := &http.Server{Addr: ":8080"}
app.Run(iris.Server(server))

/*-------------------------------------*/

// 还有更好的用法就是使用自定义的`net.Listener`

listener, err := net.Listen("tcp4", ":8080")
if err != nil {
	panic(err)
}
app.Run(iris.Listener(listener))
```
- 关闭服务并停止默认行为

```go
package main

import (
	"context"
	"github.com/kataras/iris/v12"
	"time"
)

func main() {
	app := iris.New().
		iris.RegisterOnInterrupt(func() {
		timeout := 5 * time.Second
		ctx, cancel := context.WithTimeout(context.Background(), timeout)
		defer cancel()
		app.Shutdown(ctx)
	})
	app.Get("/", func(ctx iris.Context) {
		ctx.HTML("<h1>Closed</h1>")
    })
	// iris 配置
	confit := iris.WithConfiguration(iris.Configuration{
		DisableStartupLog: true,
		Charset: "UTF-8",
    })
	app.Run(iris.Addr(":8080"), config)
}

```

3. Router

- Handler Type(请求处理器)
  - 处理过程：响应Http请求 -> 写入响应头和数据到`Context.ResponseWriter()` -> 返回信号 -> 请求处理完成
  - 注意事项：
    - 提前读取`Context.Request().Body`中的数据，因为**在写入`Context.ResponseWriter()`后无法访问`Context.Request().Body`**
    - Handler不因改变传入的Context
    - 服务器出现`panic(异常)`，服务器会认为当前的panic的影响与运行的请求无关。会重启当前的panic，并且记录栈追踪日志到服务器错误日志同时关闭连接。
- 行为
  - `iris`默认接受和注册形如`/api/login`的路径路由，并且尾部不带斜杠。
  - 如果尝试访问`/api/login/`，将会自动永久重定向到`/api/login`
- API
  - 参数：(HTTP方法，请求的路径，多个`iris.Handler`)
```go
app := iris.New()

app.Handle("GET", "/contact", func(ctx iris.Context) {
	ctx.HTML("<h1>Hello World</h1>")
})

app.Get("/", func(ctx iris.Context) {
	ctx.HTML("<h1>hello</h1>")
})
```
- 路由组（`party`）
  - 通过对路由的路径前缀进行分组，共享相同的中间件和模板。
  - 写法1：
```go
app := iris.New()
users := app.Party("/user", handler)
users.Get("/{id:uint64}/info", handler1)
user.Get("/login", handler2)
```
  - 写法2：
```go
app.PartyFunc("/user", func(user, iris.Party) {
	user.Use(AuthMiddleware)
	user.Get("/lgoin", handler2)
})
```
- 路径参数
  - `/user/{id: string}`：`user/*`
  - `/user/{name: path}` ：`/user/**/*`
- 中间件（执行过程类似与`nodejs Express`框架）
  - 中间件仅是一个 Handler 格式的函数 `func(ctx iris.Context)`，当前一个中间件调用 `ctx.Next()` 方法时，此中间件被执行，这可以用作身份验证，即如果请求验证通过，就调用 `ctx.Next()` 来执行该请求剩下链上的处理器，否则触发一个错误响应。
- 处理`http`错误
  - Iris 内建支持 HTTP APIs 的错误详情。 
  - Context.Problem 编写一个 JSON 或者 XML 问题响应，行为完全类似 Context.JSON，但是默认 ProblemOptions.JSON 的缩进是 " "，响应的 Content-type 为 application/problem+json。 
  - 使用 options.RenderXML 和 XML 字段来改变他的行为，用 application/problem+xml 的文本类型替代。

4. MVC

![](https://www.topgoer.com/static/Iris/mvc.png)

控制器结构体内部的模型(在方法函数中设置，并通过视图渲染)。你可以从一个控制器的方法中返回模型，或者在请求的声明周期中设置一个字段，在同一个请求的生命周期中的另一个方法中返回这个字段。

就像你以前使用的流程一样，MVC 程序有自己的 Router，这是 iris/router.Party 类型的，标准的 iris api Controllers 可以被注册到任何 Party 中，包括子域名，Party 的开始和完成处理器与预期的一样工作。

可选的 BeginRequest（ctx） 函数，用于在方法执行之前执行任何初始化，这对调用中间件或许多方法使用相同的数据收集很有用。

可选的 EndRequest（ctx）函数， 可在执行任何方法之后执行任何终结处理。

递归继承，例如 我们的mvc会话控制器示例具有 Session * sessions.Session 作为字段，由会话管理器的 Start 填充为MVC应用程序的动态依赖项：mvcApp.Register(sessions.New(sessions.Config{Cookie:"iris_session_id"}).Start）

通过控制器方法的输入参数访问动态路径参数，不需要绑定。当你使用 Iris 的默认语法从一个控制器中解析处理器，你需要定义方法的后缀为 By，大写字母是新的子路径。