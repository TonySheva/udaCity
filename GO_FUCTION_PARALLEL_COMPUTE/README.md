Go函数

是时候讨论一下Go的函数定义了。

什么是函数

函数，简单来讲就是一段将输入数据转换为输出数据的公用代码块。当然有的时候函数的返回值为空，那么就是说输出数据为空。而真正的处理过程在函数内部已经完成了。

想一想我们为什么需要函数，最直接的需求就是代码中有太多的重复代码了，为了代码的可读性和可维护性，将这些重复代码重构为函数也是必要的。

函数定义

先看一个例子

package main

import (
    "fmt"
)

func slice_sum(arr []int) int {
    sum := 0
    for _, elem := range arr {
        sum += elem
    }
    return sum
}

func main() {
    var arr1 = []int{1, 3, 2, 3, 2}
    var arr2 = []int{3, 2, 3, 1, 6, 4, 8, 9}
    fmt.Println(slice_sum(arr1))
    fmt.Println(slice_sum(arr2))
}
在上面的例子中，我们需要分别计算两个切片的元素和。如果我们把计算切片元素的和的代码分别为两个切片展开，那么代码就失去了简洁性和一致性。假设你预想实现同样功能的代码在拷贝粘贴的过程中发生了错误，比如忘记改变量名之类的，到时候debug到崩溃吧。因为这时很有可能你就先入为主了，因为模板代码没有错啊，是不是。所以函数就是这个用处。

我们再仔细看一下上面的函数定义：

首先是关键字func，然后后面是函数名称，参数列表，最后是返回值列表。当然如果函数没有参数列表或者返回值，那么这两项都是可选的。其中返回值两边的括号在只声明一个返回值类型的时候可以省略。

命名返回值

Go的函数很有趣，你甚至可以为返回值预先定义一个名称，在函数结束的时候，直接一个return就可以返回所有的预定义返回值。例如上面的例子，我们将sum作为命名返回值。

package main

import (
    "fmt"
)

func slice_sum(arr []int) (sum int) {
    sum = 0
    for _, elem := range arr {
        sum += elem
    }
    return
}

func main() {
    var arr1 = []int{1, 3, 2, 3, 2}
    var arr2 = []int{3, 2, 3, 1, 6, 4, 8, 9}
    fmt.Println(slice_sum(arr1))
    fmt.Println(slice_sum(arr2))
}
这里要注意的是，如果你定义了命名返回值，那么在函数内部你将不能再重复定义一个同样名称的变量。比如第一个例子中我们用sum:=0来定义和初始化变量sum，而在第二个例子中，我们只能用sum=0初始化这个变量了。因为:=表示的是定义并且初始化变量。

实参数和虚参数

可能你听说过函数的实参数和虚参数。其实所谓的实参数就是函数调用的时候传入的参数。在上面的例子中，实参就是arr1和arr2，而虚参数就是函数定义的时候表示函数需要传入哪些参数的占位参数。在上面的例子中，虚参就是arr。实参和虚参的名字不必是一样的。即使是一样的，也互不影响。因为虚参是函数的内部变量。而实参则是另一个函数的内部变量或者是全局变量。它们的作用域不同。如果一个函数的虚参碰巧和一个全局变量名称相同，那么函数使用的也是虚参。例如我们再修改一下上面的例子。

package main

import (
    "fmt"
)

var arr = []int{1, 3, 2, 3, 2}

func slice_sum(arr []int) (sum int) {
    sum = 0
    for _, elem := range arr {
        sum += elem
    }
    return
}

func main() {
    var arr2 = []int{3, 2, 3, 1, 6, 4, 8, 9}
    fmt.Println(slice_sum(arr))
    fmt.Println(slice_sum(arr2))
}
在上面的例子中，我们定义了全局变量arr并且初始化值，而我们的slice_sum函数的虚参也是arr，但是程序同样正常工作。

函数多返回值

记不记得你在java或者c里面需要返回多个值时还得去定义一个对象或者结构体的呢？在Go里面，你不需要这么做了。Go函数支持你返回多个值。

其实函数的多返回值，我们在上面遇见过很多次了。那就是range函数。这个函数用来迭代数组或者切片的时候返回的是两个值，一个是数组或切片元素的索引，另外一个是数组或切片元素。在上面的例子中，因为我们不需要元素的索引，所以我们用一个特殊的忽略返回值符号下划线(_)来忽略索引。

假设上面的例子我们除了返回切片的元素和，还想返回切片元素的平均值，那么我们修改一下代码。

package main

import (
    "fmt"
)

func slice_sum(arr []int) (int, float64) {
    sum := 0
    avg := 0.0
    for _, elem := range arr {
        sum += elem
    }
    avg = float64(sum) / float64(len(arr))
    return sum, avg
}

func main() {
    var arr1 = []int{3, 2, 3, 1, 6, 4, 8, 9}
    fmt.Println(slice_sum(arr1))
}
很简单吧，当然我们还可以将上面的参数定义为命名参数

package main

import (
    "fmt"
)

func slice_sum(arr []int) (sum int, avg float64) {
    sum = 0
    avg = 0.0
    for _, elem := range arr {
        sum += elem
    }
    avg = float64(sum) / float64(len(arr))
    //return sum, avg
    return
}

func main() {
    var arr1 = []int{3, 2, 3, 1, 6, 4, 8, 9}
    fmt.Println(slice_sum(arr1))
}
在上面的代码里面，将return sum, avg给注释了而直接使用return。其实这两种返回方式都可以。

变长参数

想一想我们的fmt包里面的Println函数，它怎么知道你传入的参数个数呢？

package main

import (
    "fmt"
)

func main() {
    fmt.Println(1)
    fmt.Println(1, 2)
    fmt.Println(1, 2, 3)
}
这个要归功于Go的一大特性，支持可变长参数列表。

首先我们来看一个例子

package main

import (
    "fmt"
)

func sum(arr ...int) int {
    sum := 0
    for _, val := range arr {
        sum += val
    }
    return sum
}
func main() {
    fmt.Println(sum(1))
    fmt.Println(sum(1, 2))
    fmt.Println(sum(1, 2, 3))
}
在上面的例子中，我们将原来的切片参数修改为可变长参数，然后使用range函数迭代这些参数，并求和。 从这里我们可以看出至少一点那就是可变长参数列表里面的参数类型都是相同的（如果你对这句话表示怀疑，可能是因为你看到Println函数恰恰可以输出不同类型的可变参数，这个问题的答案要等到我们介绍完Go的接口后才行）。

另外还有一点需要注意，那就是可变长参数定义只能是函数的最后一个参数。比如下面的例子：

package main

import (
    "fmt"
)

func sum(base int, arr ...int) int {
    sum := base
    for _, val := range arr {
        sum += val
    }
    return sum
}
func main() {
    fmt.Println(sum(100, 1))
    fmt.Println(sum(200, 1, 2))
    fmt.Println(sum(300, 1, 2, 3))
}
这里不知道你是否觉得这个例子其实和那个切片的例子很像啊，在哪里呢？

package main

import (
    "fmt"
)

func sum(base int, arr ...int) int {
    sum := base
    for _, val := range arr {
        sum += val
    }
    return sum
}
func main() {
    var arr1 = []int{1, 2, 3, 4, 5}
    fmt.Println(sum(300, arr1...))
}
呵呵，就是把切片“啪，啪，啪”三个耳光打碎了，传递过去啊！:-P

闭包函数

曾经使用python和javascript的时候就在想，如果有一天可以把这两种语言的特性做个并集该有多好。

这一天终于来了，Go支持闭包函数。

首先看一个闭包函数的例子。所谓闭包函数就是将整个函数的定义一气呵成写好并赋值给一个变量。然后用这个变量名作为函数名去调用函数体。

我们将刚刚的例子修改一下：

package main

import (
    "fmt"
)

func main() {
    var arr1 = []int{1, 2, 3, 4, 5}

    var sum = func(arr ...int) int {
        total_sum := 0
        for _, val := range arr {
            total_sum += val
        }
        return total_sum
    }
    fmt.Println(sum(arr1...))
}
从这里我们可以看出，其实闭包函数也没有什么特别之处。因为Go不支持在一个函数的内部再定义一个嵌套函数，所以使用闭包函数能够实现在一个函数内部定义另一个函数的目的。

这里我们需要注意的一个问题是，闭包函数对它外层的函数中的变量具有访问和修改的权限。例如：

package main

import (
    "fmt"
)

func main() {
    var arr1 = []int{1, 2, 3, 4, 5}
    var base = 300
    var sum = func(arr ...int) int {
        total_sum := 0
        total_sum += base
        for _, val := range arr {
            total_sum += val
        }
        return total_sum
    }
    fmt.Println(sum(arr1...))
}
这个例子，输出315，因为total_sum加上了base的值。

package main

import (
    "fmt"
)

func main() {
    var base = 0
    inc := func() {
        base += 1
    }
    fmt.Println(base)
    inc()
    fmt.Println(base)
}
在上面的例子中，闭包函数修改了main函数的局部变量base。

最后我们来看一个闭包的示例，生成偶数序列。

package main

import (
    "fmt"
)

func createEvenGenerator() func() uint {
    i := uint(0)
    return func() (retVal uint) {
        retVal = i
        i += 2
        return
    }
}
func main() {
    nextEven := createEvenGenerator()
    fmt.Println(nextEven())
    fmt.Println(nextEven())
    fmt.Println(nextEven())
}
这个例子很有意思的，因为我们定义了一个返回函数定义的函数。而所返回的函数定义就是在这个函数的内部定义的闭包函数。这个闭包函数在外层函数调用的时候，每次都生成一个新的偶数（加2操作）然后返回闭包函数定义。

其中func() uint就是函数createEvenGenerator的返回值。在createEvenGenerator中，这个返回值是return返回的闭包函数定义。

func() (retVal uint) {
        retVal = i
        i += 2
        return
    }
因为createEvenGenerator函数返回的是一个函数定义，所以我们再把它赋值给一个代表函数的变量，然后用这个代表闭包函数的变量去调用函数执行。

递归函数

每次谈到递归函数，必然绕不开阶乘和斐波拉切数列。

阶乘

package main

/**
    n!=1*2*3*...*n
*/
import (
    "fmt"
)

func factorial(x uint) uint {
    if x == 0 {
        return 1
    }
    return x * factorial(x-1)
}

func main() {
    fmt.Println(factorial(5))
}
如果x为0，那么返回1，因为0!=1。如果x是1，那么f(1)=1f(0)，如果x是2，那么f(2)=2f(1)=21f(0)，依次推断f(x)=x(x-1)...21*f(0)。

从上面看出所谓递归，就是在函数的内部重复调用一个函数的过程。需要注意的是这个函数必须能够一层一层分解，并且有出口。上面的例子出口就是0。

斐波拉切数列

求第N个斐波拉切元素

package main

/**
    f(1)=1
    f(2)=2
    f(n)=f(n-2)+f(n-1)
*/
import (
    "fmt"
)

func fibonacci(n int) int {
    var retVal = 0
    if n == 1 {
        retVal = 1
    } else if n == 2 {
        retVal = 2
    } else {
        retVal = fibonacci(n-2) + fibonacci(n-1)
    }
    return retVal

}
func main() {
    fmt.Println(fibonacci(5))
}
斐波拉切第一个元素是1，第二个元素是2，后面的元素依次是前两个元素的和。

其实对于递归函数来讲，只要知道了函数的出口，后面的不过是让计算机去不断地推断，一直推断到这个出口。理解了这一点，递归就很好理解了。

异常处理

当你读取文件失败而退出的时候是否担心文件句柄是否已经关闭？抑或是你对于try...catch...finally的结构中finally里面的代码和try里面的return代码那个先执行这样的问题痛苦不已？

一切都结束了。一门完美的语言必须有一个清晰的无歧义的执行逻辑。

好，来看看Go提供的异常处理。

defer

package main

import (
    "fmt"
)

func first() {
    fmt.Println("first func run")
}
func second() {
    fmt.Println("second func run")
}

func main() {
    defer second()
    first()
}
Go语言提供了关键字defer来在函数运行结束的时候运行一段代码或调用一个清理函数。上面的例子中，虽然second()函数写在first()函数前面，但是由于使用了defer标注，所以它是在main函数执行结束的时候才调用的。

所以输出结果

first func run
second func run
defer用途最多的在于释放各种资源。比如我们读取一个文件，读完之后需要释放文件句柄。

package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
)

func main() {
    fname := "D:\\Temp\\test.txt"
    f, err := os.Open(fname)
    defer f.Close()
    if err != nil {
        os.Exit(1)
    }
    bReader := bufio.NewReader(f)
    for {
        line, ok := bReader.ReadString('\n')
        if ok != nil {
            break
        }
        fmt.Println(strings.Trim(line, "\r\n"))
    }
}
在上面的例子中，我们按行读取文件，并且输出。从代码中，我们可以看到在使用os包中的Open方法打开文件后，立马跟着一个defer语句用来关闭文件句柄。这样就保证了该文件句柄在main函数运行结束的时候或者异常终止的时候一定能够被释放。而且由于紧跟着Open语句，一旦养成了习惯，就不会忘记去关闭文件句柄了。

panic & recover

当你周末走在林荫道上，听着小歌，哼着小曲，很是惬意。突然之间，从天而降瓢泼大雨，你顿时慌张（panic）起来，没有带伞啊，淋着雨感冒就不好了。于是你四下张望，忽然发现自己离地铁站很近，那里有很多卖伞的，心中顿时又安定了下来（recover），于是你飞奔过去买了一把伞（defer）。
好了，panic和recover是Go语言提供的用以处理异常的关键字。panic用来触发异常，而recover用来终止异常并且返回传递给panic的值。（注意recover并不能处理异常，而且recover只能在defer里面使用，否则无效。）

先瞧个小例子

package main

import (
    "fmt"
)

func main() {
    fmt.Println("I am walking and singing...")
    panic("It starts to rain cats and dogs")
    msg := recover()
    fmt.Println(msg)
}
看看输出结果

runtime.panic(0x48d380, 0xc084003210)
    C:/Users/ADMINI~1/AppData/Local/Temp/2/bindist667667715/go/src/pkg/runtime/panic.c:266  +0xc8
main.main()
    D:/JemyGraw/Creation/Go/freebook_go/func_d1.go:9 +0xea
exit status 2
咦？怎么没有输出recover获取的错误信息呢？

这是因为在运行到panic语句的时候，程序已经异常终止了，后面的代码就不运行了。

那么如何才能阻止程序异常终止呢？这个时候要使用defer。因为defer一定是在函数执行结束的时候运行的。不管是正常结束还是异常终止。

修改一下代码

package main

import (
    "fmt"
)

func main() {
    defer func() {
        msg := recover()
        fmt.Println(msg)
    }()
    fmt.Println("I am walking and singing...")
    panic("It starts to rain cats and dogs")
}
好了，看下输出

I am walking and singing...
It starts to rain cats and dogs
小结：

panic触发的异常通常是运行时错误。比如试图访问的索引超出了数组边界，忘记初始化字典或者任何无法轻易恢复到正常执行的错误。

转载: https://github.com/jemygraw/TechDoc/blob/master/Go%E8%BD%BB%E6%9D%BE%E5%AD%A6/go_tutorial_6_func.md


Go并行计算

如果说Go有什么让人一见钟情的特性，那大概就是并行计算了吧。

做个题目

如果我们列出10以下所有能够被3或者5整除的自然数，那么我们得到的是3，5，6和9。这四个数的和是23。 那么请计算1000以下（不包括1000）的所有能够被3或者5整除的自然数的和。
这个题目的一个思路就是：

(1) 先计算1000以下所有能够被3整除的整数的和A，
(2) 然后计算1000以下所有能够被5整除的整数和B，
(3) 然后再计算1000以下所有能够被3和5整除的整数和C,
(4) 使用A+B-C就得到了最后的结果。

按照上面的方法，传统的方法当然就是一步一步计算，然后再到第(4)步汇总了。

但是一旦有了Go，我们就可以让前面三个步骤并行计算，然后再在第(4)步汇总。

并行计算涉及到一个新的数据类型chan和一个新的关键字go。

先看例子：

package main

import (
    "fmt"
    "time"
)

func get_sum_of_divisible(num int, divider int, resultChan chan int) {
    sum := 0
    for value := 0; value < num; value++ {
        if value%divider == 0 {
            sum += value
        }
    }
    resultChan <- sum
}

func main() {
    LIMIT := 1000
    resultChan := make(chan int, 3)
    t_start := time.Now()
    go get_sum_of_divisible(LIMIT, 3, resultChan)
    go get_sum_of_divisible(LIMIT, 5, resultChan)
    go get_sum_of_divisible(LIMIT, 15, resultChan)

    sum3, sum5, sum15 := <-resultChan, <-resultChan, <-resultChan
    sum := sum3 + sum5 - sum15
    t_end := time.Now()
    fmt.Println(sum)
    fmt.Println(t_end.Sub(t_start))
}
(1) 在上面的例子中，我们首先定义了一个普通的函数get_sum_of_divisible，这个函数的最后一个参数是一个整型chan类型，这种类型，你可以把它当作一个先进先出的队列。你可以向它写入数据，也可以从它读出数据。它所能接受的数据类型就是由chan关键字后面的类型所决定的。在上面的例子中，我们使用<-运算符将函数计算的结果写入channel。channel是go提供的用来协程之间通信的方式。本例中main是一个协程，三个get_sum_of_divisible调用是协程。要在这四个协程间通信，必须有一种可靠的手段。

(2) 在main函数中，我们使用go关键字来开启并行计算。并行计算是由goroutine来支持的，goroutine又叫做协程，你可以把它看作为比线程更轻量级的运算。开启一个协程很简单，就是go关键字后面跟上所要运行的函数。

(3) 最后，我们要从channel中取出并行计算的结果。使用<-运算符从channel里面取出数据。

在本例中，我们为了演示go并行计算的速度，还引进了time包来计算程序执行时间。在同普通的顺序计算相比，并行计算的速度是非同凡响的。

好了，上面的例子看完，我们来详细讲解Go的并行计算。

Goroutine协程

所谓协程，就是Go提供的轻量级的独立运算过程，比线程还轻。创建一个协程很简单，就是go关键字加上所要运行的函数。看个例子：

package main

import (
    "fmt"
)

func list_elem(n int) {
    for i := 0; i < n; i++ {
        fmt.Println(i)
    }
}
func main() {
    go list_elem(10)
}
上面的例子是创建一个协程遍历一下元素。但是当你运行的时候，你会发现什么都没有输出！为什么呢？ 因为上面的main函数在创建完协程后就立刻退出了，所以协程还没有来得及运行呢！修改一下：

package main

import (
    "fmt"
)

func list_elem(n int) {
    for i := 0; i < n; i++ {
        fmt.Println(i)
    }
}
func main() {
    go list_elem(10)
    var input string
    fmt.Scanln(&input)
}
这里，我们在main函数创建协程后，要求用户输入任何数据后才退出，这样协程就有了运行的时间，故而输出结果：

0
1
2
3
4
5
6
7
8
9
其实在开头的例子里面，我们的main函数事实上也被阻塞了，因为sum3, sum5, sum15 := <-resultChan, <-resultChan, <-resultChan这行代码在channel里面没有数据或者数据个数不符的时候，都会阻塞在那里，直到协程结束，写入结果。

不过既然是并行计算，我们还是得看看协程是否真的并行计算了。

package main

import (
    "fmt"
    "math/rand"
    "time"
)

func list_elem(n int, tag string) {
    for i := 0; i < n; i++ {
        fmt.Println(tag, i)
        tick := time.Duration(rand.Intn(100))
        time.Sleep(time.Millisecond * tick)
    }
}
func main() {
    go list_elem(10, "go_a")
    go list_elem(20, "go_b")
    var input string
    fmt.Scanln(&input)
}
输出结果

go_a 0
go_b 0
go_a 1
go_b 1
go_a 2
go_b 2
go_b 3
go_b 4
go_a 3
go_b 5
go_b 6
go_a 4
go_a 5
go_b 7
go_a 6
go_a 7
go_b 8
go_b 9
go_a 8
go_b 10
go_b 11
go_a 9
go_b 12
go_b 13
go_b 14
go_b 15
go_b 16
go_b 17
go_b 18
go_b 19
在上面的例子中，我们让两个协程在每输出一个数字的时候，随机Sleep了一会儿。如果是并行计算，那么输出是无序的。从上面的例子中，我们可以看出两个协程确实并行运行了。

Channel通道

Channel提供了协程之间的通信方式以及运行同步机制。

假设训练定点投篮和三分投篮，教练在计数。
package main

import (
    "fmt"
    "time"
)

func fixed_shooting(msg_chan chan string) {
    for {
        msg_chan <- "fixed shooting"
        fmt.Println("continue fixed shooting...")
    }
}

func count(msg_chan chan string) {
    for {
        msg := <-msg_chan
        fmt.Println(msg)
        time.Sleep(time.Second * 1)
    }
}

func main() {
    var c chan string
    c = make(chan string)

    go fixed_shooting(c)
    go count(c)

    var input string
    fmt.Scanln(&input)
}
输出结果为：

fixed shooting
continue fixed shooting...
fixed shooting
continue fixed shooting...
fixed shooting
continue fixed shooting...
我们看到在fixed_shooting函数里面我们将消息传递到channel，然后输出提示信息"continue fixed shooting..."，而在count函数里面，我们从channel里面取出消息输出，然后间隔1秒再去取消息输出。这里面我们可以考虑一下，如果我们不去从channel中取消息会出现什么情况？我们把main函数里面的go count(c)注释掉，然后再运行一下。发现程序再也不会输出消息和提示信息了。这是因为channel中根本就没有信息了，因为如果你要向channel里面写信息，必须有配对的取信息的一端，否则是不会写的。

我们再把三分投篮加上。

package main

import (
    "fmt"
    "time"
)

func fixed_shooting(msg_chan chan string) {
    for {
        msg_chan <- "fixed shooting"
    }
}

func three_point_shooting(msg_chan chan string) {
    for {
        msg_chan <- "three point shooting"
    }
}

func count(msg_chan chan string) {
    for {
        msg := <-msg_chan
        fmt.Println(msg)
        time.Sleep(time.Second * 1)
    }
}

func main() {
    var c chan string
    c = make(chan string)

    go fixed_shooting(c)
    go three_point_shooting(c)
    go count(c)

    var input string
    fmt.Scanln(&input)
}
输出结果为：

fixed shooting
three point shooting
fixed shooting
three point shooting
fixed shooting
three point shooting
我们看到程序交替输出定点投篮和三分投篮，这是因为写入channel的信息必须要读取出来，否则尝试再次写入就失败了。

在上面的例子中，我们发现定义一个channel信息变量的方式就是多加一个chan关键字。并且你能够向channel写入数据和从channel读取数据。这里我们还可以设置channel通道的方向。

Channel通道方向*

所谓的通道方向就是写和读。如果我们如下定义

c chan<- string //那么你只能向channel写入数据
而这种定义

c <-chan string //那么你只能从channel读取数据
试图向只读chan变量写入数据或者试图从只写chan变量读取数据都会导致编译错误。

如果是默认的定义方式

c chan string //那么你既可以向channel写入数据也可以从channnel读取数据
多通道(Select)

如果上面的投篮训练现在有两个教练了，各自负责一个训练项目。而且还在不同的篮球场，这个时候很显然，我们一个channel就不够用了。修改一下：

package main

import (
    "fmt"
    "time"
)

func fixed_shooting(msg_chan chan string) {
    for {
        msg_chan <- "fixed shooting"
        time.Sleep(time.Second * 1)
    }
}

func three_point_shooting(msg_chan chan string) {
    for {
        msg_chan <- "three point shooting"
        time.Sleep(time.Second * 1)
    }
}

func main() {
    c_fixed := make(chan string)
    c_3_point := make(chan string)

    go fixed_shooting(c_fixed)
    go three_point_shooting(c_3_point)

    go func() {
        for {
            select {
            case msg1 := <-c_fixed:
                fmt.Println(msg1)
            case msg2 := <-c_3_point:
                fmt.Println(msg2)
            }
        }

    }()

    var input string
    fmt.Scanln(&input)
}
其他的和上面的一样，唯一不同的是我们将定点投篮和三分投篮的消息写入了不同的channel，那么main函数如何知道从哪个channel读取消息呢？使用select方法，select方法依次检查每个channel是否有消息传递过来，如果有就取出来输出。如果同时有多个消息到达，那么select闭上眼睛随机选一个channel来从中读取消息，如果没有一个channel有消息到达，那么select语句就阻塞在这里一直等待。

在某些情况下，比如学生投篮中受伤了，那么就轮到医护人员上场了，教练在一般看看，如果是重伤，教练就不等了，就回去了休息了，待会儿再过来看看情况。我们可以给select加上一个case用来判断是否等待各个消息到达超时。

package main

import (
    "fmt"
    "time"
)

func fixed_shooting(msg_chan chan string) {
    var times = 3
    var t = 1
    for {
        if t <= times {
            msg_chan <- "fixed shooting"
        }
        t++
        time.Sleep(time.Second * 1)
    }
}

func three_point_shooting(msg_chan chan string) {
    var times = 5
    var t = 1
    for {
        if t <= times {
            msg_chan <- "three point shooting"
        }
        t++
        time.Sleep(time.Second * 1)
    }
}

func main() {
    c_fixed := make(chan string)
    c_3_point := make(chan string)

    go fixed_shooting(c_fixed)
    go three_point_shooting(c_3_point)

    go func() {
        for {
            select {
            case msg1 := <-c_fixed:
                fmt.Println(msg1)
            case msg2 := <-c_3_point:
                fmt.Println(msg2)
            case <-time.After(time.Second * 5):
                fmt.Println("timeout, check again...")
            }
        }

    }()

    var input string
    fmt.Scanln(&input)
}
在上面的例子中，我们让投篮的人在几次过后挂掉，然后教练就每次等5秒出来看看情况（累死丫的，:-P），因为我们对等待的时间不感兴趣就不用变量存储了，直接<-time.After(time.Second*5)，或许你会奇怪，为什么各个channel消息都没有到达，select为什么不阻塞？就是因为这个time.After，虽然它没有显式地告诉你这是一个channel消息，但是记得么？main函数也是一个channel啊！哈哈！至于time.After的功能实际上让main阻塞了5秒后返回给main的channel一个时间。所以我们在case里面把这个时间消息读出来，select就不阻塞了。

输出结果如下：

fixed shooting
three point shooting
fixed shooting
three point shooting
fixed shooting
three point shooting
three point shooting
three point shooting
timeout, check again...
timeout, check again...
timeout, check again...
timeout, check again...
这里select还有一个default的选项，如果你指定了default选项，那么当select发现没有消息到达的时候也不会阻塞，直接开始转回去再次判断。

Channel Buffer通道缓冲区

我们定义chan变量的时候，还可以指定它的缓冲区大小。一般我们定义的channel都是同步的，也就是说接受端和发送端彼此等待对方ok才开始。但是如果你给一个channel指定了一个缓冲区，那么消息的发送和接受式异步的，除非channel缓冲区已经满了。

c:=make(chan int, 1)
我们看个例子：

package main

import (
    "fmt"
    "strconv"
    "time"
)

func shooting(msg_chan chan string) {
    var group = 1
    for {
        for i := 1; i <= 10; i++ {
            msg_chan <- strconv.Itoa(group) + ":" + strconv.Itoa(i)
        }
        group++
        time.Sleep(time.Second * 10)
    }
}

func count(msg_chan chan string) {
    for {
        fmt.Println(<-msg_chan)
    }
}

func main() {
    var c = make(chan string, 20)
    go shooting(c)
    go count(c)

    var input string
    fmt.Scanln(&input)
}
输出结果为：

1:1
1:2
1:3
1:4
1:5
1:6
1:7
1:8
1:9
1:10
2:1
2:2
2:3
2:4
2:5
2:6
2:7
2:8
2:9
2:10
3:1
3:2
3:3
3:4
3:5
3:6
3:7
3:8
3:9
3:10
4:1
4:2
4:3
4:4
4:5
4:6
4:7
4:8
4:9
4:10
你可以尝试运行一下，每次都是一下子输出10个数据。然后等待10秒再输出一批。

小结

并行计算这种特点最适合用来开发网站服务器，因为一般网站服务都是高并发的，逻辑十分复杂。而使用Go的这种特性恰是提供了一种极好的方法。

转载： https://github.com/jemygraw/TechDoc/blob/master/Go%E8%BD%BB%E6%9D%BE%E5%AD%A6/go_tutorial_9_parallel_compute.md
