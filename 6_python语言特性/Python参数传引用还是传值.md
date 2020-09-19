<!-- TOC -->

- [1. all](#1-all)
    - [1.1. conclusion](#11-conclusion)
    - [1.2. 可以通过id()来取得对象的身份](#12-%E5%8F%AF%E4%BB%A5%E9%80%9A%E8%BF%87id%E6%9D%A5%E5%8F%96%E5%BE%97%E5%AF%B9%E8%B1%A1%E7%9A%84%E8%BA%AB%E4%BB%BD)
    - [1.3. 可以通过type()来取得a引用对象的数据类型](#13-%E5%8F%AF%E4%BB%A5%E9%80%9A%E8%BF%87type%E6%9D%A5%E5%8F%96%E5%BE%97a%E5%BC%95%E7%94%A8%E5%AF%B9%E8%B1%A1%E7%9A%84%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)
    - [1.4. 对象的值](#14-%E5%AF%B9%E8%B1%A1%E7%9A%84%E5%80%BC)

<!-- /TOC -->

# all
<a id="markdown-all" name="all"></a>




## conclusion
<a id="markdown-conclusion" name="conclusion"></a>

**Python函数传递的是对象的引用值，非传值或传引用**。

**但是如果对象是不可变的，感觉和c语言中传值差不多。**

**如果对象是可变的，感觉和c语言中传引用差不多**。

Python程序中存储的所有数据都是对象，每一个对象有一个身份，一个类型和一个值。

看变量的实际作用，执行a = 8 这行代码时，就会创建一个值为8的int对象。

变量名是对这个"一个值为8的int对象"的引用。（也可以简称a绑定到8这个对象）

## 可以通过id()来取得对象的身份
<a id="markdown-%E5%8F%AF%E4%BB%A5%E9%80%9A%E8%BF%87id()%E6%9D%A5%E5%8F%96%E5%BE%97%E5%AF%B9%E8%B1%A1%E7%9A%84%E8%BA%AB%E4%BB%BD" name="%E5%8F%AF%E4%BB%A5%E9%80%9A%E8%BF%87id()%E6%9D%A5%E5%8F%96%E5%BE%97%E5%AF%B9%E8%B1%A1%E7%9A%84%E8%BA%AB%E4%BB%BD"></a>

这个内置函数，它的参数是a这个变量名，这个函数返回的值

是这个变量a引用的那个"一个值为8的int对象"的内存地址。
```py
   >>> a = 8
   >>> id(8)
   4298180944

   >>> help(id)
   Help on built-in function id in module builtins:
```
id(obj, /)
    Return the identity of an object.

    This is guaranteed to be unique among simultaneously existing objects.
    (CPython uses the object's memory address.)


## 可以通过type()来取得a引用对象的数据类型
<a id="markdown-%E5%8F%AF%E4%BB%A5%E9%80%9A%E8%BF%87type()%E6%9D%A5%E5%8F%96%E5%BE%97a%E5%BC%95%E7%94%A8%E5%AF%B9%E8%B1%A1%E7%9A%84%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B" name="%E5%8F%AF%E4%BB%A5%E9%80%9A%E8%BF%87type()%E6%9D%A5%E5%8F%96%E5%BE%97a%E5%BC%95%E7%94%A8%E5%AF%B9%E8%B1%A1%E7%9A%84%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B"></a>
```py
       >>> a = 8
       >>> id(8)
       4298180944
       >>> type(a)
       <class 'int'>
```

## 对象的值
<a id="markdown-%E5%AF%B9%E8%B1%A1%E7%9A%84%E5%80%BC" name="%E5%AF%B9%E8%B1%A1%E7%9A%84%E5%80%BC"></a>

当变量出现在表达式中，它会被它引用的对象的值替代。

总结：类型是属于对象，而不是变量。变量只是对对象的一个引用。

对象有可变对象和不可变对象之分。



Python函数传递参数到底是传值还是引用？

传值、引用这个是c/c++中的概念，Python中一切都是对象，

实参向形参传递的是对象的引用值。就像Python赋值的意思。

请看代码
```py
# coding:utf-8

def foo(a):
   print(f"传来是对象的引用 对象地址为{id(a)}")
   a = 3 #形式参数a是局部变量、a重新绑定到3这个对象。
   print("变量a新引用对象地址为{0}".format(id(a)))
   print(a)

x = 5
print("全局变量x引用的对象地址为{0}".format(id(x)))
foo(x)
print(x)
#由于函数内部a绑定到新的对象，也就修改不了全部变量x引用的对象5
#全局变量x引用的对象地址为4298181192
#传来是对象的引用 对象地址为4298181192
#变量a新引用对象地址为4298181240
#3
#5
```

```py
# coding:utf-8

def foo(a):
   """在函数内部直接修改了同一个引用指向的对象。
   也就修改了实际参数传来的引用值指向的对象。
   """
   a.append("can change object")
   return a

lst = [1,2,3]
print(foo(lst))
print(lst)
#[1, 2, 3, 'can change object']
#[1, 2, 3, 'can change object']
```

```py
# coding:utf-8

def foo(a):
   """实际参数传来一个对象[1,2,3]的引用，当时形式参数（局部变量a
   重新引用到新的对象，也就是说保存了新的对象）
   当然不能修改原来的对象了。
   """
   a = ["黄哥Python培训","黄哥"]
   return a

lst = [1,2,3]
for item in foo(lst):
   print(item)
print(lst)
```
