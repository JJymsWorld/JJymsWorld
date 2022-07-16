---
title: C++中struct与class的区别
date: 2022-07-16 16:43:23
tags: 
 - [C++]
categories:
 - [gallery]
---

1. 在C语言中，struct是一种数据类型，内部不能定义函数，所以在面向C编程的过程中，struct不能包含任何函数，否则编译器报错。
2. 在C++中，struct得到了很大的扩充，可以包含成员函数，可以实现继承，可以实现多态

## struct和class的区别

- 默认的继承访问权方面。class默认的是private， struct默认的是public。class可以继承struct，struct也可以继承class，默认的继承访问权取决于子类。
- 默认访问权限。struct作为数据结构的实现体，它默认的数据访问控制是public的，而class作为对象的实现体，它默认的成员变量访问控制是private的。
- struct更适合实现数据结构体，class更适合看成对象的实现体。
- 在{}上使用的区别。class和struct如果定义了构造函数的话，都不能够使用大括号进行初始化。如果没有定义构造函数，struct可以使用大括号进行初始化。如果没有构造函数，且所有成员变量全是public的话，class可以用大括号初始化。
