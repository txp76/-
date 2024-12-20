## **makefile 学习笔记**

1.基本格式

目标: *依赖项*
    命令

例如：hello: main.o    

​           gcc main.o -o hello

2.变量定义

变量名=值；

#### makefile文件示例：

`objects = main.o kbd.o command.o display.o \`
    `insert.o search.o files.o utils.o`

`edit : $(objects)`
    `cc -o edit $(objects)`
`main.o : main.c defs.h`
    `cc -c main.c`
`kbd.o : kbd.c defs.h command.h`
    `cc -c kbd.c`
`command.o : command.c defs.h command.h`
    `cc -c command.c`
`display.o : display.c defs.h buffer.h`
    `cc -c display.c`
`insert.o : insert.c defs.h buffer.h`
    `cc -c insert.c`
`search.o : search.c defs.h buffer.h`
    `cc -c search.c`
`files.o : files.c defs.h buffer.h command.h`
    `cc -c files.c`
`utils.o : utils.c defs.h`
    `cc -c utils.c`

`.PHONY : clean          //伪目标文件`

`clean :`
    `rm edit $(objects)`

clean:后面可以加上你想做的一些事情，如果你想看到在编译完后看看main.c的源代码，你可以在加上cat这个命令，例子如下：

> ```
> clean:
>     cat main.c
>     rm -f *.o
> ```
>
> #### 搜寻文件
>
> ```
> VPATH = src:../headers
> ```
>
> 上面的定义指定两个目录，“src”和“../headers”，make会按照这个顺序进行搜索。目录由“冒号”分隔。（当然，当前目录永远是最高优先搜索的地方）

## 显示命令

通常，make会把其要执行的命令行在命令执行前输出到屏幕上。当我们用 `@` 字符在命令行前，那么，这个命令将不被make显示出来

## 定义命令包

如果Makefile中出现一些相同命令序列，那么我们可以为这些相同的命令序列定义一个变量。定义这种命令序列的语法以 `define` 开始，以 `endef` 结束，如:

```
define run-yacc
yacc $(firstword $^)
mv y.tab.c $@
endef
```

条件表达式的语法为:

```
<conditional-directive>
<text-if-true>
endif
```

以及:

```
<conditional-directive>
<text-if-true>
else
<text-if-false>
endif
```

其中 `<conditional-directive>` 表示条件关键字，如 `ifeq` 。这个关键字有四个。

第一个是我们前面所见过的 `ifeq`

```
ifeq (<arg1>, <arg2>)
ifeq '<arg1>' '<arg2>'
ifeq "<arg1>" "<arg2>"
ifeq "<arg1>" '<arg2>'
ifeq '<arg1>' "<arg2>"
```

比较参数 `arg1` 和 `arg2` 的值是否相同。当然，参数中我们还可以使用make的函数。如:

```
ifeq ($(strip $(foo)),)
<text-if-empty>
endif
```

这个示例中使用了 `strip` 函数，如果这个函数的返回值是空（Empty），那么 `<text-if-empty>` 就生效。

第二个条件关键字是 `ifneq` 。语法是：

```
ifneq (<arg1>, <arg2>)
ifneq '<arg1>' '<arg2>'
ifneq "<arg1>" "<arg2>"
ifneq "<arg1>" '<arg2>'
ifneq '<arg1>' "<arg2>"
```

其比较参数 `arg1` 和 `arg2` 的值是否相同，如果不同，则为真。和 `ifeq` 类似。

第三个条件关键字是 `ifdef` 。语法是：

```
ifdef <variable-name>
```

如果变量 `<variable-name>` 的值非空，那到表达式为真。否则，表达式为假。当然， `<variable-name>` 同样可以是一个函数的返回值。注意， `ifdef` 只是测试一个变量是否有值，其并不会把变量扩展到当前位置。还是来看两个例子：

示例一：

```
bar =
foo = $(bar)
ifdef foo
    frobozz = yes
else
    frobozz = no
endif
```

示例二：

```
foo =
ifdef foo
    frobozz = yes
else
    frobozz = no
endif
```

第一个例子中， `$(frobozz)` 值是 `yes` ，第二个则是 `no`。

第四个条件关键字是 `ifndef` 。其语法是：

```
ifndef <variable-name>
```

这个我就不多说了，和 `ifdef` 是相反的意思。

在 `<conditional-directive>` 这一行上，多余的空格是被允许的，但是不能以 `Tab` 键作为开始（不然就被认为是命令）。而注释符 `#` 同样也是安全的。 `else` 和 `endif` 也一样，只要不是以 `Tab` 键开始就行了。