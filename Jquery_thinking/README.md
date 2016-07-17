jQuery整体框架甚是复杂，也不易读懂，这几日一直在研究这个笨重而强大的框架。jQuery的总体架构可以分为：入口模块、底层模块和功能模块。这里，我们以jquery-1.7.1为例进行分析。

jquery的总体架构
16 (function( window, undefined ) {
         // 构造 jQuery 对象
  22     var jQuery = (function() {
  25         var jQuery = function( selector, context ) {
  27                 return new jQuery.fn.init( selector, context, rootjQuery );
  28             },
                 // 一堆局部变量声明
  97         jQuery.fn = jQuery.prototype = {
  98             constructor: jQuery,
  99             init: function( selector, context, rootjQuery ) { ... },
                 // 一堆原型属性和方法
 319         };
 322         jQuery.fn.init.prototype = jQuery.fn;
 324         jQuery.extend = jQuery.fn.extend = function() { ... };
 388         jQuery.extend({
                 // 一堆静态属性和方法
 892         });
 955         return jQuery;
 957     })();
          // 省略其他模块的代码 ...
9246     window.jQuery = window.$ = jQuery;
9266 })( window );
分析一下以上代码，我们发现jquery采取了匿名函数自执行的写法，这样做的好处就是可以有效的防止命名空间与变量污染的问题。缩写一下以上代码就是：

(function(window, undefined) {
    var jQuery = function() {}
    // ...
    window.jQuery = window.$ = jQuery;
})(window);
参数window
匿名函数传了两个参数进来，一个是window，一个是undefined。我们知道，在js中变量是有作用域链的，这两个变量的传入就会变成匿名函数的局部变量，访问起来的时候速度会更快。通过传入window对象可以使window对象作为局部变量使用，那么，函数的参数也都变成了局部变量，当在jquery中访问window对象时，就不需要将作用域链退回到顶层作用域，从而可以更快的访问window对象。

参数undefined
js在查找变量的时候，js引擎首先会在函数自身的作用域中查找这个变量，如果没有的话就继续往上找，找到了就返回该变量，找不到就返回undefined。undefined是window对象的一个属性，通过传入undefined参数，但又不进行赋值，可以缩短查找undefined时的作用域链。在 自调用匿名函数 的作用域内，确保undefined是真的未定义。因为undefined能够被重写，赋予新的值。

jquery.fn是啥？
 jQuery.fn = jQuery.prototype = {
              constructor: jQuery,
              init: function( selector, context, rootjQuery ) { ... },
                 // 一堆原型属性和方法
        };
通过分析以上代码，我们发现jQuery.fn即是jQuery.prototype，这样写的好处就是更加简短吧。之后，我们又看到jquery为了简洁，干脆使用一个$符号来代替jquery使用，因此，在我们使用jquery框架的使用经常都会用到$()，

构造函数jQuery()


jQuery的对象并不是通过 new jQuery 创建的，而是通过 new jQuery.fn.init 创建的：

var jQuery = function( selector, context ) {

       return new jQuery.fn.init( selector, context, rootjQuery );

}
这里定义了一个变量jQuery，他的值是jQuery构造函数，在955行（最上面的代码）返回并赋值给jQuery变量

jQuery.fn.init
jQuery.fn （上面97行）是构造函数jQuery()的原型对象，jQuery.fn.init()是jQuery原型方法，也可以称作构造函数。负责解析参数selector和context的类型并执行相应的查找。

参数context：可以不传入，或者传入jQuery对象，DOM元素，普通js对象之一
参数rootjQuery：包含了document对象的jQuery对象，用于document.getElementById()查找失败等情况。

jQuery.fn.init.prototype = jQuery.fn = jQuery.prototype
jQuery(selector [,context])

默认情况下，对匹配元素的查找从根元素document 对象开始，即查找范围是整个文档树，不过也可以传入第二个参数context来限定它的查找范围。例如：

$('div.foo').click(function () {
            $('span',this).addClass('bar');//限定查找范围,即上面的context
   });
$.extend()和$.fn.extend()
方法jQuery.extend(object)和jQuery.fn.extend(object)用于合并两个或多个对象到第一个对象。相关源代码如下（部分）：

jQuery.extend = jQuery.fn.extend = function() {
    var options, name, src, copy, copyIsArray, clone,//定义的一组局部变量
        target = arguments[0] || {},
        i = 1,
        length = arguments.length,
        deep = false;
相关变量含义如下：

变量options：指向某个源对象
变量name:表示目标对象的某个属性名
变量src：表示目标对象的某个属性的原始值
变量copy：表示某个源对象的某个属性的值
变量copyIsArray：指示变量copy是否是数组
变量clone：表示深度复制时原始值的修正值
变量target：指向目标对象
变量i：表示源对象的起始下标
变量length：表示参数的个数，用于修正变量
变量deep：指示是否执行深度复制，默认为false
jQuery.extend(object);　为jQuery类添加添加类方法，可以理解为添加静态方法。如：

$.extend({ 
　　add:function(a,b){
        return a+b;
    } 
}); 
便为　jQuery　添加一个为add　的　“静态方法”，之后便可以在引入 jQuery　的地方，使用这个方法了， 就是将add方法合并到jquery的全局对象中。

$.add(3,4); //return 7 
jQuery.fn.extend(object)，查看一段官网的代码演示如下：

<label><input type="checkbox" name="foo"> Foo</label>
<label><input type="checkbox" name="bar"> Bar</label>

<script>
    jQuery.fn.extend({
        check: function() {
            return this.each(function() {
                this.checked = true;
            });
        },
        uncheck: function() {
            return this.each(function() {
                this.checked = false;
            });
        }
    });
    // Use the newly created .check() method
    $( "input[type='checkbox']" ).check();
</script>
看下面一个jQuery对象的扩展方法：

 $.fn.extend({
      sayHello : function(){
        alert('hello');
    }
 });
 
就是将sayHello方法合并到jquery的实例对象中

CSS选择器引擎 Sizzle
可以说，jQuery是为操作DOM而诞生的，jQuery之所以如此强大，得益于CSS选择器引擎 Sizzle，解析规则引用网上的一段实例：

selector："div > p + div.aaron input[type="checkbox"]"

解析规则：
1 按照从右到左
2 取出最后一个token  比如[type="checkbox"]
                            {
                                matches : Array[3]
                                type    : "ATTR"
                                value   : "[type="
                                checkbox "]"
                            }
3 过滤类型 如果type是 > + ~ 空 四种关系选择器中的一种，则跳过，在继续过滤
4 直到匹配到为 ID,CLASS,TAG  中一种 , 因为这样才能通过浏览器的接口索取
5 此时seed种子合集中就有值了,这样把刷选的条件给缩的很小了
6 如果匹配的seed的合集有多个就需要进一步的过滤了,修正选择器 selector: "div > p + div.aaron [type="checkbox"]"
7 OK,跳到一下阶段的编译函数
deferred对象
开发网站的过程中，我们经常遇到某些耗时很长的javascript操作。其中，既有异步的操作（比如ajax读取服务器数据），也有同步的操作（比如遍历一个大型数组），它们都不是立即能得到结果的。

通常的做法是，为它们指定回调函数（callback）。即事先规定，一旦它们运行结束，应该调用哪些函数。

但是，在回调函数方面，jQuery的功能非常弱。为了改变这一点，jQuery开发团队就设计了deferred对象。

简单说，deferred对象就是jQuery的回调函数解决方案。在英语中，defer的意思是"延迟"，所以deferred对象的含义就是"延迟"到未来某个点再执行。

回顾一下jQuery的ajax操作的传统写法：

$.ajax({
 
　　　url: "test.html",
 
　　　success: function(){
　　　　　alert("哈哈，成功了！");
　　　},
 
　　　error:function(){
　　　　　alert("出错啦！");
　　　}
 

　});
在上面的代码中，$.ajax()接受一个对象参数，这个对象包含两个方法：success方法指定操作成功后的回调函数，error方法指定操作失败后的回调函数。

$.ajax()操作完成后，如果使用的是低于1.5.0版本的jQuery，返回的是XHR对象，你没法进行链式操作；如果高于1.5.0版本，返回的是deferred对象，可以进行链式操作。

现在，新的写法是这样的：

$.ajax("test.html")
 
　　.done(function(){ alert("哈哈，成功了！"); })
 
　　.fail(function(){ alert("出错啦！"); });
为多个操作指定回调函数
deferred对象的另一大好处，就是它允许你为多个事件指定一个回调函数，这是传统写法做不到的。

请看下面的代码，它用到了一个新的方法$.when()：

$.when($.ajax("test1.html"), $.ajax("test2.html"))
 
　.done(function(){ alert("哈哈，成功了！"); })
 
　.fail(function(){ alert("出错啦！"); });
这段代码的意思是，先执行两个操作$.ajax("test1.html")和$.ajax("test2.html")，如果都成功了，就运行done()指定的回调函数；如果有一个失败或都失败了，就执行fail()指定的回调函数。

jQuery.Deferred( func ) 的实现原理
内部维护了三个回调函数列表：成功回调函数列表、失败回调函数列表、消息回调函数列表，其他方法则围绕这三个列表进行操作和检测。

jQuery.Deferred( func ) 的源码结构：

 jQuery.extend({
 
    Deferred: function( func ) {
            // 成功回调函数列表
        var doneList = jQuery.Callbacks( "once memory" ),
            // 失败回调函数列表
            failList = jQuery.Callbacks( "once memory" ),
            // 消息回调函数列表
            progressList = jQuery.Callbacks( "memory" ),
            // 初始状态
            state = "pending",
            // 异步队列的只读副本
            promise = {
                // done, fail, progress
                // state, isResolved, isRejected
                // then, always
                // pipe
                // promise           
            },
            // 异步队列
            deferred = promise.promise({}),
            key;
        // 添加触发成功、失败、消息回调函列表的方法
        for ( key in lists ) {
            deferred[ key ] = lists[ key ].fire;
            deferred[ key + "With" ] = lists[ key ].fireWith;
        }
        // 添加设置状态的回调函数
        deferred.done( function() {
            state = "resolved";
        }, failList.disable, progressList.lock )
        .fail( function() {
            state = "rejected";
        }, doneList.disable, progressList.lock );
        // 如果传入函数参数 func，则执行。
        if ( func ) {
            func.call( deferred, deferred );
        }
 
        // 返回异步队列 deferred
        return deferred;
    },
}
         
 
jQuery.when( deferreds )
 
提供了基于一个或多个对象的状态来执行回调函数的功能，通常是基于具有异步事件的异步队列。
jQuery.when( deferreds ) 的用法
 
如果传入多个异步队列对象，方法 jQuery.when() 返回一个新的主异步队列对象的只读副本，只读副本将跟踪所传入的异步队列的最终状态。
 
一旦所有异步队列都变为成功状态，“主“异步队列的成功回调函数被调用；
 
如果其中一个异步队列变为失败状态，主异步队列的失败回调函数被调用。
 
 
/*
请求 '/when.do?method=when1' 返回 {"when":1}
请求 '/when.do?method=when2' 返回 {"when":2}
请求 '/when.do?method=when3' 返回 {"when":3}
*/
var whenDone = function(){ console.log( 'done', arguments ); },
    whenFail = function(){ console.log( 'fail', arguments ); };
$.when(
    $.ajax( '/when.do?method=when1', { dataType: "json" } ),
    $.ajax( '/when.do?method=when2', { dataType: "json" } ),
    $.ajax( '/when.do?method=when3', { dataType: "json" } )
).done( whenDone ).fail( whenFail );


异步队列 Deferred

解耦异步任务和回调函数

为 ajax 模块、队列模块、ready 事件提供基础功能。

原型属性和方法
原型属性和方法源代码：

 
  97 jQuery.fn = jQuery.prototype = {
  98     constructor: jQuery,
  99     init: function( selector, context, rootjQuery ) {}
 210     selector: "",
 213     jquery: "1.7.1",
 216     length: 0,
 219     size: function() {},
 223     toArray: function() {},
 229     get: function( num ) {},
 241     pushStack: function( elems, name, selector ) {},
 270     each: function( callback, args ) {},
 274     ready: function( fn ) {}, //
 284     eq: function( i ) {},
 291     first: function() {},
 295     last: function() {},
 299     slice: function() {},
 304     map: function( callback ) {},
 310     end: function() {},
 316     push: push,
 317     sort: [].sort,
 318     splice: [].splice
 319 };
属性selector用于记录jQuery查找和过滤DOM元素时的选择器表达式。
属性.length表示当前jquery对象中元素的个数。
方法.size()返回当前jquery对象中元素的个数，功能上等同于属性length，但应该优先使用length，因为他没有函数调用开销。

.size()源码如下：

size()：function(){
    return this.length;
}
方法.toArray()将当前jQuery对象转换为真正的数组，转换后的数组包含了所有元素，其源码如下：

toArray: function() {
        return slice.call( this );
    },
方法.get(index)返回当前jQuery对象中指定位置的元素，或包含了全部元素的数组。其源
码如下：

    get: function( num ) {
        return num == null ?

            // Return a 'clean' array
            this.toArray() :

            // Return just the object
            ( num < 0 ? this[ this.length + num ] : this[ num ] );
    },
如果没有传入参数，则调用.toArray()返回了包含有锁元素的数组；如果指定了参数index，则返回一个单独的元素，index从0开始计数，并且支持负数。

首先会判断num是否小于0，如果小于0，则用length+num重新计算下标，然后使用数组访问操作符（[]）获取指定位置的元素，这是支持下标为负数的一个小技巧；如果大于等于0，直接返回指定位置的元素。

eg()和get()使用详解：http://segmentfault.com/blog/trigkit4/1190000000660257#articleHeader3

方法.each()用于遍历当前jQuery对象，并在每个元素上执行回调函数。方法.each()内部通过简单的调用静态方法jQuery.each()实现：

each: function( callback, args ) {
        return jQuery.each( this, callback, args );
    },
回调函数是在当前元素为上下文的语境中触发的，即关键字this总是指向当前元素，在回调函数中return false 可以终止遍历。

方法.map()遍历当前jQuery对象，在每个元素上执行回调函数，并将回调函数的返回值放入一个新jQuery对象中。该方法常用于获取或设置DOM元素集合的值。

map: function( callback ) {
        return this.pushStack( jQuery.map(this, function( elem, i ) {
            return callback.call( elem, i, elem );
        }));
    },
原型方法.pushStack()创建一个新的空jQuery对象，然后把DOM元素集合放入这个jQuery对象中，并保留对当前jQuery对象的引用。

原型方法.pushStack()是核心方法之一，它为以下方法提供支持：

jQuery对象遍历：.eq()、.first()、.last()、.slice()、.map()。

DOM查找、过滤：.find()、.not()、.filter()、.closest()、.add()、.andSelf()。

DOM遍历：.parent()、.parents()、.parentsUntil()、.next()、.prev()、.nextAll()、.prevAll()、.nextUnit()、.prevUnit()、.siblings()、.children()、.contents()。

DOM插入：jQuery.before()、jQuery.after()、jQuery.replaceWith()、.append()、.prepent()、.before()、.after()、.replaceWith()。
定义方法.push( elems, name, selector )，它接受3个参数：

参数elems：将放入新jQuery对象的元素数组（或类数组对象）。

参数name：产生元素数组elems的jQuery方法名。

参数selector：传给jQuery方法的参数，用于修正原型属性.selector。
方法.end()结束当前链条中最近的筛选操作，并将匹配元素还原为之前的状态

end: function() {
        return this.prevObject || this.constructor(null);
    },
返回前一个jQuery对象，如果属性prevObect不存在，则构建一个空的jQuery对象返回。方法.pushStack()用于入栈，方法.end()用于出栈

静态属性和方法
相关源码如下：

388 jQuery.extend({
 389     noConflict: function( deep ) {},
 402     isReady: false,
 406     readyWait: 1,
 409     holdReady: function( hold ) {},
 418     ready: function( wait ) {},
 444     bindReady: function() {},
 492     isFunction: function( obj ) {},
 496     isArray: Array.isArray || function( obj ) {},
 501     isWindow: function( obj ) {},
 505     isNumeric: function( obj ) {},
 509     type: function( obj ) {},
 515     isPlainObject: function( obj ) {},
 544     isEmptyObject: function( obj ) {},
 551     error: function( msg ) {},
 555     parseJSON: function( data ) {},
 581     parseXML: function( data ) {},
 601     noop: function() {},
 606     globalEval: function( data ) {},
 619     camelCase: function( string ) {},
 623     nodeName: function( elem, name ) {},
 628     each: function( object, callback, args ) {},
 669     trim: trim ? function( text ) {} : function( text ) {},
 684     makeArray: function( array, results ) {},
 702     inArray: function( elem, array, i ) {},
 724     merge: function( first, second ) {},
 744     grep: function( elems, callback, inv ) {},
 761     map: function( elems, callback, arg ) {},
 794     guid: 1,
 798     proxy: function( fn, context ) {},
 825     access: function( elems, key, value, exec, fn, pass ) {},
 852     now: function() {},
 858     uaMatch: function( ua ) {},
 870     sub: function() {},
 891     browser: {}
 892 });
方法jQuery.noConflict([removeAll])用于释放jQuery对全局变量$的控制权，可选参数removeAll指示是否释放对全局变量jQuery的控制权。
在使用jQuery的同时，如果需要使用另一个javascript库，可以调用$.noConflict()返回$给其他库

方法jQuery.isFunction(obj)用于判断传入的参数是否是函数；
方法jQuery.isArray(obj)用于判断传入的参数是否是数组。

这两个方法的实现依赖于jQuer.type(obj),通过判断jQuery.type(obj)返回值是否是function和array来实现
相关源码如下：

492  isFunction : function(obj){
493        return jQuery.type(obj) === "function";
494     },
495  
496     isArray: Array.isArray || function(obj){
497        return jQuery.type(obj) === "array";
498     },
方法jQuery.type(obj)用于判断参数的内建JavaScript类型。如果参数是undefined 或 null ，返回 undefined 或 null；如果参数是JavaScript内部对象，则返回对象
的字符串名称；其他情况一律返回object，相关源码如下：

509  type :function(obj){
510        return obj == null ?
511          String( obj ) :
512          class2type[toString.call(obj)] || "object";
513    },
方法jQuery.isWindow(obj)用于判断传入的参数是否是window对象，通过检测是否存在特征属性setInterval来实现，相关代码如下：

全选复制放进笔记 
501        isWindow: function(obj){
502            return obj && typeof obj ==="object" && "setInterval" in obj;
503        },

转自 http://segmentfault.com/blog/trigkit4/1190000000660257#articleHeader3
