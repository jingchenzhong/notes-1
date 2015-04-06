---
tags: cs
title: The programming language
date: 2015-02-27 15:46:14
category: cs
---
# The c programming language
##Reference
C11
C Traps and Pitfalls
Expert C Programming

#integer constant

##compiler, assembler, linker and loader: a brief story
http://www.tenouk.com/ModuleW.html

##Type
* Object type and function type

###Object -- void, scalar, composite

###Scalar type -- Arithmetic types and pointer types are collectively called scalar types.
[What does Scalar mean?](http://www.techopedia.com/definition/16441/scalar)
scalar 词源上由scale演化而来, scalar type来自scalar processor and vector processor.
超标量也是来自这里concurrent SISD =>MIMD 所谓的流水线.

###Arithmetic type -- Integer and floating types are collectively called arithmetic types.

##Incomplete and complete
void; struct s; union u;

##Conversion 
* only effect to scalar type data
error: conversion to non-scalar type requested

* 无符号数 转换成upper rank的有符号数, 还是无符号数, 不管最初是-1!
unsigned int i = 1; long f = -10;if ( i > f ) printf("ok\n"); true 证明确实usigned 向long转换了.
unsigned int i = -3; long f = -5u;if ( i > f ) printf("ok\n"); true
c11上是ok的!

* 做signed 和unsigned 貌似both convert to unsigned gcc才给警告, 否则不给即便是signed和unsigned比较. 
##Integer Promotion
[Deep C: Integer Promotion](http://www.idryman.org/blog/2012/11/21/integer-promotion/)
* [Integral Promotions](https://msdn.microsoft.com/en-us/library/fc9te331.aspx)
0xF int
0xFFFFFFFF unsigned int

##Scopes of identifiers
For each different entity that an identifier designates, the identifier is visible (i.e., can be
used) only within a region of program text called its scope.
* four kinds of scopes: function, file, block, and function prototype
If the declarator or type specifier that declares the identifier
appears outside of any block or list of parameters, the identifier has file scope, which
terminates at the end of the translation unit.

If the declarator or type specifier that
declares the identifier appears inside a block or within the list of parameter declarations in
a function definition, the identifier has block scope, which terminates at the end of the
associated block.

If the declarator or type specifier that declares the identifier appears
within the list of parameter declarations in a function prototype (not part of a function
definition), the identifier has function prototype scope, which terminates at the end of the
function declarator.

If an identifier designates two different entities in the same name
space, the scopes might overlap.

##linkages of identifiers
An identifier declared in different scopes or in the same scope more than once can be
made to refer to the same object or function by a process called linkage.
* There are three kinds of linkage: external, internal, and none.
* There is no linkage between different identifiers.

* external linkage
In the set of translation units and libraries that constitutes an entire program, each
declaration of a particular identifier with external linkage denotes the same object or
function. --c11

If the name has external linkage, 
the entity that name denotes may be referred to from another translation unit 
using a distinct declaration for that same name, 
and from other scopes within the same translation unit using distinct declarations. --wikipedia

*Internal linkage
Within one translation unit, each declaration of an identifier with internal
linkage denotes the same object or function. --c11

Were the name given internal linkage, 
such a declaration would denote a distinct entity, although using the same name, 
but its entity could be referred to by distinct declarations within the same translation unit.  --wikipedia

* No linkage
Each declaration of an identifier with no linkage denotes a unique entity. --c11
A name that has no linkage at all cannot be referred to from declarations in different scopes, 
not even from within the same translation unit. --wikipedia

## Name spaces of identifiers

## Storage durations of objects
An object has a storage duration that determines its lifetime.
There are four storage durations: static, thread, automatic, and allocated.
The lifetime of an object is the portion of program execution during which storage is
guaranteed to be reserved for it.
In book 21st Century C, it was named memory mode.

* static storage duration
An object whose identifier is declared without the storage-class specifier _Thread_local, 
and either with external or internal linkage or with the storage-class specifier static(for no linkage --firo), 
has static storage duration.

Its lifetime is the entire execution of the program and its stored value is initialized only once, 
prior to program startup.

* automatic storage duration
An object whose identifier is declared with no linkage and without the storage-class
specifier static has automatic storage duration, as do some compound literals.

* alloced storage duration
The lifetime of an allocated object extends from the allocation until the deallocation.

* Thread storage duration
details in c11

## Alignment of objects
An alignment is an implementation-defined integer value representing the number of bytes between
successive addresses at which a given object can be allocated.
struct x { 
    short a;
    int c;
    short b;
    short d;
    long m;
    short y;
};//Fedora 21 x86_64
sizeof(x) = 32
1. basic alignment is long == 8, if delete m, basic alignment is 4;
2. basic alignment only can be scalar type.


##Declarations
A declaration specifies the interpretation and attributes of a set of identifiers.

A definition of an identifier is a declaration for that identifier that:
for an object, causes storage to be reserved for that object;
for a function, includes the function body;
for an enumeration constant, is the (only) declaration of the identifier;
for a typedef name, is the first (or only) declaration of the identifier.

The declaration specifiers consist of a sequence of specifiers that indicate the linkage,
storage duration, and part of the type of the entities that the declarators denote.

##Marco
[如果#操作符出现在对象宏的替换列表中,则仅作为一个普通字符,不具有下述含义](http://blog.csdn.net/huyansoft/article/details/2484297)

##lexical pitfall
## Greedy lexical analysis

* Write tokens with blank!
x = y/*p;  /* oops, hidden error.*/
* Associative
You donot need to remember it! Just use it!
* Precedence
Need to remember, but if you not sure, parenthess.

##Common concepts
* Every C variable declaration has two parts: a type and a list of expression-like things called declarators.
* A declarator looks something like an expression that is expected to evaluate to the given type.
* C compiler to translate C programs into terms that make sense to the linker.
* One important job of a linker is to handle these name conflicts.
* Expression?
* Satement?

##FAQ
* Logical operation with signed value? x86!
int c = 0xFFFFFFFF;  int d = c >> 31; => d == f;
unsigned c = 0xFFFFFFFF;  int d = c >> 31; d!= f;
* shift beyond bits long?
c >> 296 == c >> 8
* Diffences between strlen() and sizeof()?

#ASM
bdi 4, 8 delay solt
mips instruction size is fixed, 32bit, 4byte.
instruction address:  instrction in hex formate	   instruction in string formate, 260
8002c28c:   8c440104    lw  a0,260(v0)

##lwr & lwl
load a word
different with endianess
register 63.....32......0
big endian: lwl high bits in b + 0
little endian: lwl high in b + off
向中心

##endianess
ar71xx big
ralink little
cpu -> toolchain