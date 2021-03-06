---
tags: [ cs ] 
title: The programming language
date: 2015-02-27T15:46:14+08:00 
category: cs
---

[我为什么不再做PL人](http://www.yinwang.org/blog-cn/2016/03/31/no-longer-pl)
[Functors, Applicatives, And Monads In Pictures](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)
[Learn Type Theory](http://jozefg.bitbucket.org/posts/2015-08-14-learn-tt.html)

∅ ⊢ pl
scope: The set of expressions for which a binding defines a name is called the scope of that name.
# Reference
http://www.zhihu.com/question/21843639
貌似正则是type 3, c是type2, 范式角度我现在不好理解, 缺乏语言学的知识.
Programming Language Pragmatics 3rd
C11
C Traps and Pitfalls
Expert C Programming
# Contents
Programming language theory
Programming language
# Programming language theory 
What is formal language and the relationg between fl and pl?
Language primitive
## Semantics
# C 
我们还是从使用的角度来看c 语言; 
c语言自身重要基础性质, 我们需要了解.
除此之外, 我们还要了解一些重要的标准库函数.
知道这些, 我们就能用c语言.
## Type
* Object type and function type
* Object -- void, scalar,aggregate types, composite
* Scalar type -- Arithmetic types and pointer types
[What does Scalar mean?](http://www.techopedia.com/definition/16441/scalar)
scalar 词源上由scale演化而来, scalar type来自scalar processor and vector processor.
超标量也是来自这里concurrent SISD =>MIMD 所谓的流水线.
* Arithmetic type -- Integer and floating types
## Incomplete and complete
void; struct s; union u;
## Conversion 
* only effect to scalar type data
error: conversion to non-scalar type requested
* 无符号数 转换成upper rank的有符号数, 还是无符号数, 不管最初是-1!
unsigned int i = 1; long f = -10;if ( i > f ) printf("ok\n"); true 证明确实usigned 向long转换了.
unsigned int i = -3; long f = -5u;if ( i > f ) printf("ok\n"); true
c11上是ok的!
* 做signed 和unsigned 貌似both convert to unsigned gcc才给警告, 否则不给即便是signed和unsigned比较. 
## lvalue rvalue modfiable rvalue
[Understanding lvalues and rvalues in C and C++](http://eli.thegreenplace.net/2011/12/15/understanding-lvalues-and-rvalues-in-c-and-c)
If a variable/expression  has a address, it's of lvalue.
## Array decay
数组退化的初衷, 可能是K&R当年计算资源紧缺, 导致不允许函数传值copy数组内容.
总之标准委员会介入之前就决定.
[[C] [原创]数组与指针---都是"退化"惹的祸](http://bbs.chinaunix.net/thread-1031622-1-1.html)
关于char *s错误声明的讲解不错!
[Exception to array not decaying into a pointer?](http://stackoverflow.com/questions/17752978/exception-to-array-not-decaying-into-a-pointer)
在c11 6.3.2.1 Lvalues, arrays, and function designators第3点说明了
array不会退化的4种场景. Except when it is 
* the operand of the sizeof operator, 
* the _Alignof operator, or the
* unary & operator, or 
* is a string literal used to initialize an array, an expression that has type 
‘‘array of type’’ is converted to an expression with type ‘‘pointer to type’’ 
that points to the initial element of the array object and is not an lvalue. 
If the array object has register storage class, the behavior is undefined.
这4种场景之外, 那么array名退化的结果是pointer.
这个pointer和我们最常用的pointer如int *p有什么区别呢?
首先array decay是type上的转化array -> pointer.
其次数组名原来是lvalue -> not an lvalue更谈不上modifiable.
type: tyepof(array[0]) * 
value: &array[0] or array
property: not lvalue
当然这只是c11上的说明, 我们只要明白为什么不能修改一个decay数组名就行了.
实现的个人猜测是: 根据lvalue的定义decay后数组名还是lvalue, 只不过一直都不是modifiable.

* 为什么作为函数形参的数组名可以++, 而作为变量的数组名就不可以.
因为形参数组名被当初pointer处理modifiable lvalue, 而实参数组名只是传值而已.
而普通数组名是一个lvalue,不能修改.
### Why innermost dimension can be omit in array
用不到.
In essence, all arrays in C are one-dimensional.

Because the array will decay to pointer and to calculate offset to 
the elements of the array you do not need to know the innermost dimension. 

Compiler has to know by how much to increment the pointer when 
indexing on the first dimension for example. So if an int array is named a,
## Integer Promotion
[Deep C: Integer Promotion](http://www.idryman.org/blog/2012/11/21/integer-promotion/)
* [Integral Promotions](https://msdn.microsoft.com/en-us/library/fc9te331.aspx)
0xF int
0xFFFFFFFF unsigned int
## Scopes of identifiers
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
## linkages of identifiers
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
* Internal linkage
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
More deatils in cs.md
## Declarations
A declaration specifies the interpretation and attributes of a set of identifiers.
A definition of an identifier is a declaration for that identifier that:
for an object, causes storage to be reserved for that object;
for a function, includes the function body;
for an enumeration constant, is the (only) declaration of the identifier;
for a typedef name, is the first (or only) declaration of the identifier.
The declaration specifiers consist of a sequence of specifiers that indicate the linkage,
storage duration, and part of the type of the entities that the declarators denote.
## Marco
[如果#操作符出现在对象宏的替换列表中,则仅作为一个普通字符,不具有下述含义](http://blog.csdn.net/huyansoft/article/details/2484297)
## Expressions
* cast
A cast does not yield an lvalue.
## Lexical element
### Character constants
An integer character constant has type int.
## lexical pitfall
## Greedy lexical analysis
* Write tokens with blank!
x = y/*p;  /* oops, hidden error.*/
* Associative
You donot need to remember it! Just use it!
* Precedence
Need to remember, but if you not sure, parenthess.
## inline 和宏的区别
inline的好处与坏处
没有调用的开销效率很高, 但是调试代码复杂了, 内链函数的实现是拷贝副本消耗内存.
inline有类型检测, 宏没有.
## C standard library
## FAQ
* Logical operation with signed value? x86!
int c = 0xFFFFFFFF;  int d = c >> 31; => d == f;
unsigned c = 0xFFFFFFFF;  int d = c >> 31; d!= f;
* shift beyond bits long?
c >> 296 == c >> 8
* 变量同名不同类型不同文件, 通过extern, 否则就是multiple definition
这是c语言最为迷糊的地方.简单说来类似union, 具体我也不清楚.
1. 所有名字引用同一个object
2. 不同文件中不同类型的sizeof和当前文件类型一直.

# C standard library
NULL?
## String handling
kernel/lib/string.c
* Copying functions
memcpy: dest
memmove: dest
strcpy: dest, 拷贝\0
strncpy: dest, if src_len >= n; 0 NULL, if src _len < n; (n - len) NULL;
strlcpy: src_len, mini(n -1, src_len -1) + \0, src_len 用你返回啊!
* Concatenation functions
strcat: dest, overwrite dest \0 with src util src \0
strncat: dest, 末尾一定有\0
* Comparison functions
memcmp: 差值,
strcmp: -1, 0, 1, 如果整个s1都比完了NULL or --n ==0 for strncmp, return 0
* Search functions
memchr, NULL or p;
strchr,ditto, 到\0返回NUll
strrchr,ditto, last occurence.
strspn: s1开始有多少在s2中.
strcspn: s1开始有多少不在s2中.
strpbrk: s2中第一次出现的位置
strstr: 找子串, 用memcmp
# ASM in c code
c语言嵌入汇编这不是c语言的特性是编译器的feature.
[How to Use Inline Assembly Language in C Code](https://gcc.gnu.org/onlinedocs/gcc/Using-Assembly-Language-with-C.html#Using-Assembly-Language-with-C)
#MIPS
bdi 4, 8 delay solt
mips instruction size is fixed, 32bit, 4byte.
instruction address:  instrction in hex formate	   instruction in string formate, 260
8002c28c:   8c440104    lw  a0,260(v0)
##ASM codes Fixme
move <-
j long jump
b short jump
a0, a1...99% is parameters.
##lwr & lwl
load a word
different with endianess
register 63.....32......0
big endian: lwl high bits in b + 0
little endian: lwl high in b + off
向中心
# x86
[Intel’s ‘cmpxchg’ instruction](http://heather.cs.ucdavis.edu/~matloff/50/PLN/lock.pdf)
eax: e stand for 32
rax: r stand for 64
[lock prefix in x86](http://x86.renejeschke.de/html/file_module_x86_id_159.html)
the lock prefix make instruction atomic!
## registers' purpose
## Register
* gs
The linux kernel uses GS to access cpu-specific memory.
* gdtr
GDT
* tr
TSS addressing
#Wildcards
[A wildcard character is a type of meta character](http://whatis.techtarget.com/definition/wildcard-character)
##Type
### Standard Wildcards (globbing patterns)
File and directory patterns
?: must stand for a character
* Regular expression
* SQL
#Shell
## test
### -n is not equivalent to ! -z
Be caution! just juse -z and !-z
## ls 
-l: show hardlinks of file/dir in 2nd column
drwsrwsr-T: T stand for sticky bit no other execution bit
##eval
* create variable name
	__var="name"
	eval "export -- \"$__var=firo\""
	set | grep firo
	__var='name'
	bbb='firo'
##expr
expr "$name" : '\(.*\)\.conf'
##find
* find symbols in object 
	find . -name 'a.out' -exec nm -D {} \; -print
	find . -name '*.o' -print0 | xargs -0 nm -A | egrep ' (i|y)$'
* rm
find . -name ‘your_pattern*’ -exec rm -f {} \;
find . -name ‘your_pattern*’ -delete
## grep
-c: count of match
##pipe
find . -type d | while read d; do cnt=$(ls $d | grep tgz | wc -l); echo "$cnt $d"; done | sort -n >stat 
#AWK
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
