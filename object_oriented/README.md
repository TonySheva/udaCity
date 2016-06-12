python的面向对象

Class: A user-defined prototype for an object that defines a set of attributes 
that characterize any object of the class. The attributes are data members 
(class variables and instance variables) and methods, accessed via dot notation.

Class variable: A variable that is shared by all instances of a class. Class 
variables are defined within a class but outside any of the class's methods. 
Class variables are not used as frequently as instance variables are.

类变量也叫静态变量，也就是在变量前加了static 的变量；
实例变量也叫对象变量，即没加static 的变量；
区别在于：
类变量和实例变量的区别在于：类变量是所有对象共有，其中一个对象将它值改变，
其他对象得到的就是改变后的结果；而实例变量则属对象私有，某一个对象将其值改变，不影响其他对象

@ClassMethod:
有别于实例instance来调用class里面的method（self），
cls是直接调用class里面的method。这样调用了整个class，而不是具体的某个实例

一般来说，要使用某个类的方法，需要先实例化一个对象再调用方法。
而使用@staticmethod或@classmethod，就可以不需要实例化，直接类名.方法名()来调用。
这有利于组织代码，把某些应该属于某个类的函数给放到那个类里去，同时有利于命名空间的整洁。

既然@staticmethod和@classmethod都可以直接类名.方法名()来调用，那他们有什么区别呢
从它们的使用上来看,

@staticmethod不需要表示自身对象的self和自身类的cls参数，就跟使用函数一样。

@classmethod也不需要self参数，但第一个参数需要是表示自身类的cls参数。
如果在@staticmethod中要调用到这个类的一些属性方法，只能直接类名.属性名或类名.方法名。
而@classmethod因为持有cls参数，可以来调用类的属性，类的方法，实例化对象等，避免硬编码。
class A(object):
    bar = 1
    def foo(self):
        print 'foo'
 
    @staticmethod  
    def static_foo():  
        print 'static_foo'  
        print A.bar  
 
    @classmethod  
    def class_foo(cls):  
        print 'class_foo'  
        print cls.bar  
        cls().foo()  

A.static_foo
A.class_foo

echo:
static_foo
1
class_foo
1

Data member: A class variable or instance variable that holds data associated 
with a class and its objects.

Function overloading: The assignment of more than one behavior to a particular 
function. The operation performed varies by the types of objects or arguments 
involved.

Instance variable: A variable that is defined inside a method and belongs only 
to the current instance of a class.

Inheritance: The transfer of the characteristics of a class to other classes 
that are derived from it.

Instance: An individual object of a certain class. An object obj that belongs 
to a class Circle, for example, is an instance of the class Circle.

Instantiation: The creation of an instance of a class.

Method : A special kind of function that is defined in a class definition.

Object: A unique instance of a data structure that's defined by its class. An 
object comprises both data members (class variables and instance variables) and methods.

Operator overloading: The assignment of more than one function to a particular 
operator.


继承和抽象很关键
Don't repeat yourself

step by step

