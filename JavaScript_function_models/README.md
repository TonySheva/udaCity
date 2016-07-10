
JavaScript设计模式的作用是提高代码的重用性，可读性，使代码更容易的维护和扩展

在javascript中，函数是一类对象，这表示他可以作为参数传递给其他函数；此外，函数还可以提供作用域。

js函数基础部分：JavaScript学习总结（四）function函数部分

创建函数的语法
命名函数表达式
//命名函数表达式
var add = function add(a,b){
    return a+b;
};

var foo = function bar() {
    console.log(foo === bar);
};
foo();//true
可见，他们引用的是同一函数，但这只在函数体内有效。

var foo = function bar() {};
console.log(foo === bar);//ReferenceError: bar is not defined
但是，你不能通过调用bar()来调用该函数。

var foo = (function bar() {
    console.log(foo === bar);
})();//false
函数表达式
//又名匿名函数
var add = function(a,b){
    return a+b;
};
为变量 add 赋的值是函数定义本身。这样，add 就成了一个函数，可以在任何地方调用。

函数的声明
function foo(){
    //code here
}  //这里可以不需要分号
在尾随的分号中，函数表达式应总是使用分号，而函数的声明中并不需要分号结尾。

声明式函数与函数表达式的区别在于：在JS的预编译期，声明式函数将会先被提取出来，然后才按顺序执行js代码：

console.log(f1);//[Function: f1]
console.log(f2);//undefined，Javascript并非完全的按顺序解释执行，而是在解释之前会对Javascript进行一次“预编译”，在预编译的过程中，会把定义式的函数优先执行

function f1(){
    console.log("I am f1");
}
var f2 = function (){
    console.log("I am f2");
};
由于声明函数都会在全局作用域构造时候完成，因此声明函数都是window对象的属性，这就说明为什么我们不管在哪里声明函数，声明函数最终都是属于window对象的原因了。

在javascript语言里任何匿名函数都是属于window对象。在定义匿名函数时候它会返回自己的内存地址，如果此时有个变量接收了这个内存地址，那么匿名函数就能在程序里被使用了，因为匿名函数也是在全局执行环境构造时候定义和赋值，所以匿名函数的this指向也是window对象

var f2 = function (){
    console.log("I am f2");
};
console.log(f2());//I am f2

(function(){
   console.log(this === window);//true
})();
函数声明与表达式
函数的提升（hoisting）
函数声明的行为并不等同于命名函数表达式，其区别在于提升（hoisting）行为，看下面例子：

<script type="text/javascript">
    //全局函数
    function foo(){alert("global foo!");}
    function bar(){alert('￼global bar');}
    
    function hoist(){
        console.log(typeof foo);//function
        console.log(typeof bar);//undefined
        
        foo();//local foo!
        bar();//TypeError: 'undefined' is not a function  

        //变量foo以及实现者被提升
        function foo(){
            alert('local foo!￼');
        }
        
        //仅变量bar被提升，函数实现部分 并未被提升
        var bar = function(){
            alert('￼local bar!');
        };
    }
    hoist(); 
</script>
对于所有变量，无论在函数体的何处进行声明，都会在内部被提升到函数顶部。而对于函数通用适用，其原因在于函数只是分配给变量的对象。

提升，顾名思义，就是把下面的东西提到上面。在JS中，就是把定义在后面的东西（变量或函数）提升到前面中定义。 从上面的例子可以看出，在函数hoist内部中的foo和bar移动到了顶部，从而覆盖了全局foo和bar函数。局部函数bar和foo的区别在于，foo被提升到了顶部且能正常运行，而bar()的定义并没有得到提升，仅有它的声明被提升，所以，当执行bar()的时候显示结果为undefined而不是作为函数来使用。

即时函数模式
函数也是对象，因此它们可以作为返回值。使用自执行函数的好处是直接声明一个匿名函数，立即使用，省得定义一个用一次就不用的函数，而且免了命名冲突的问题，js中没有命名空间的概念，因此很容易发生函数名字冲突，一旦命名冲突以最后声明的为准。

模式一：
<script>
    (function () {
        var a = 1;
        return function () {
            alert(2);
        };
    }()());//弹出2，第一个圆括号自执行，第二个圆括号执行内部匿名函数
</script>
模式二：自执行函数变量的指向
<script type="text/javascript">
        var result = (function () {
            return 2;
        })();//这里已执行了函数
 
        alert(result);//result 指向了由自执行函数的返回值2；如果弹出result()会出错
</script>
模式三：嵌套函数
<script type="text/javascript">
        var result = (function () {
            return function () {
                return 2;
            };
        })();
 
 alert(result());//alert(result)的时候弹出function(){return 2}
</script>
模式四：自执行函数把它的返回值赋给变量
    var abc = (function () {
            var a = 1;
            return function () {
                return ++a;
            }
        })();//自执行函数把return后面的函数返回给变量
   alert(abc());//如果是alert(abc)就会弹出return语句后面的代码；如果是abc(),则会执行return后面的函数
模式五：函数内部执行自身，递归
// 这是一个自执行的函数，函数内部执行自身，递归
function abc() { abc(); }
回调模式
回调函数：当你将一个函数write()作为一个参数传递给另一个函数call()时，那么在某一时刻call()可能会执行（或者调用）write()。这种情况下，write()就叫做回调函数（callback function）。

异步事件监听器
回调模式有许多用途，比如，当附加一个事件监听器到页面上的一个元素时，实际上是提供了一个回调函数的指针，该函数将会在事件发生时被调用。如：

document.addEventListener("click",console.log,false);
上面代码示例展示了文档单击事件时以冒泡模式传递给回调函数console.log()的

javascript特别适用于事件驱动编程，因为回调模式支持程序以异步方式运行。

超时
使用回调模式的另一个例子是，当使用浏览器的window对象所提供的超时方法：setTimeout()和setInterval()，如：

<script type="text/javascript">
    var call = function(){
        console.log("100ms will be asked…");
    };
    setTimeout(call, 100);
</script>
库中的回调模式
当设计一个js库时，回调函数将派上用场，一个库的代码应尽可能地使用可复用的代码，而回调可以帮助实现这种通用化。当我们设计一个庞大的js库时，事实上，用户并不会需要其中的大部分功能，而我们可以专注于核心功能并提供“挂钩形式”的回调函数，这将使我们更容易地构建、扩展，以及自定义库方法

Curry化
Curry化技术是一种通过把多个参数填充到函数体中，实现将函数转换为一个新的经过简化的（使之接受的参数更少）函数的技术。———【精通JavaScript】

简单来说，Curry化就是一个转换过程，即我们执行函数转换的过程。如下例子：

<script type="text/javascript">
    //curry化的add()函数
    function add(x,y){
        var oldx = x, oldy = y;
        if(typeof oldy == "undefined"){
            return function(newy){
                return oldx + newy;
            };
        }
        //完全应用
        return x+y;
    }
    //测试
    typeof add(5);//输出"function"
    add(3)(4);//7
    //创建并存储一个新函数
    var add2000 = add(2000);
    add2000(10);//输出2010
</script>
当第一次调用add()时，它为返回的内部函数创建了一个闭包。该闭包将原始的x和y值存储到私有变量oldx和oldy中。

现在，我们将可使用任意函数curry的通用方法，如：

<script type="text/javascript">
    //普通函数
    function add(x,y){
        return x + y;
    }
    //将一个函数curry化以获得一个新的函数
    var newadd = test(add,5);
    newadd(4);//9
    
    //另一种选择，直接调用新函数
    test(add,6)(7);//输出13
</script>
何时使用Curry化

