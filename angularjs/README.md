在 ECMAScript 中，变量可以存在两种类型的值，即原始值和引用值。
原始值
存储在栈（stack）中的简单数据段，也就是说，它们的值直接存储在变量访问的位置。
引用值
存储在堆（heap）中的对象，也就是说，存储在变量处的值是一个指针（point），指向存储对象的内存处。
为变量赋值时，ECMAScript 的解释程序必须判断该值是原始类型，还是引用类型。要实现这一点，解释程序则需尝试判断该值是否为 ECMAScript 的原始类型之一，即 Undefined、Null、Boolean、Number 和 String 型。由于这些原始类型占据的空间是固定的，所以可将他们存储在较小的内存区域 - 栈中。这样存储便于迅速查寻变量的值。

引用类型 和值类型的区别。

赋值 符号 = 不是单纯的copy
注意angular.copy()

var tony = ObjectA / arrayA 

实际上object 和array 是一个无限大的空间
这里的tony 不是引用类型。
当ObjectA 和arrayA 发生变化时，tony也会发生变化

而 var tony = 123 

这里的tony 就是123 ，值类型，除非你再次定义tony ，否则tony是不会改变的

这里需要注意的是不同空间才能保证互不干扰。

经常需要操作ObjectA 或者arrayA ，循环或者取值中 增加或者修改。
同时返回tony。
这里就需要var tony = copy(ObjectA / arrayA) 这样就不会随ObjectA 或者arrayA的
改变而改变。值类型。也是不同空间。

值类型1 2 3 4 ...n 引用类型也是 1 2 3 4 ...n

如果 objectB本来就不会改变，那么直接引用也是可以的。
注意值类型和引用类型的同时也考虑一下不同的空间就可以了。
若var tony = objectB 

