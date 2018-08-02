# Makefile 笔记
makefile 文件告诉 make 指令怎样去编译和链接程序。
## makefile 规则
```
target ... : prerequisites ...
  command
  ...
```
> target:可以是一个 object file(目标文件)，也可以是一个可执行文件，也可以是一个标签。

> prerequisites: 生成该target所依赖的文件

> command: 该target要执行的命令

这样就形成了一个依赖关系，当prerequisites中有文件要更新的话，command的命令就会执行

## makefile 中使用变量
声明直接用 = 赋值，使用需要 $()
```
objects = main.o kbd.o command.o display.o 

edit: $(objects)
  cc -o edit $(objects)
```

## makefile 自动推导
每一个 .o 文件都要加上对应的命令去生成它，这无疑是很麻烦的，事实上可以让 GNU 的 make 去自动推导它，make 每遇到一个 .o 文件就会把对应的 .c 文件加入依赖中，并且对应的编译语句也会被推导出来

## 清除中间文件的规则
通常情况下，如下这样写就可以了
```
clean:
  rm edit $(objects)
```
还有更为稳健的做法
```
.PHONY:clean
clean:
  -rm edit $(objects)
```
 
## 引用其他的 makefile
使用 include 关键字,例如在当前 makefile 中应用另一个 temp.mk 文件
```
include temp.mk
```
 
## make 的工作步骤
> * 读入所有的 makefile
> * 读入被 include 的其他 makefile
> * 初始化文件中的变量
> * 推导隐晦规则，分析所有规则
> * 为所有的目标文件创建依赖关系链
> * 根据依赖关系，决定那些目标要重新生成
> * 执行生成命令
 
## 通配符
支持3个通配符 * ? ~ 作用和 shell 中相同

## 文件搜寻
makefile 默认在当前目录搜寻文件，但有时我们的文件存放在不同目录下，这是需要用到特殊变量 VPATH

VPATH 中的路径使用 :  分隔，例如
```
VPATH = src:../headers
```
表示在搜寻文件时，包含 src 和 ../headers 两个目录
 
## 命令执行
如果希望上一条命令的结果作用于下一条命令，则不应过分成两行写，应写在同一行，用 : 分隔
```
exec:
  cd /home/project; pwd
```
  
## 命令出错
make 在运行时会检测每个命令的执行结果，如果有命令执行时出错，将终止执行make。

有时，对于一些命令我们并不希望出错就停止执行。例如 mkdir ,如果不存在目录则创建目录，可是当目录存在是我们希望他继续执行而不是停止。
要实现这一点，可以在命令前加上 - 号，这样不管该命令是否执行成功都不会影响 make 的继续执行。
```
clean:
  -rm -f *.o
```

## 条件判断
例如，判断变量 CC 的值是否等于 gcc
```
ifeq ($(CC),gcc)
    ……
else
    ……
endif
```
此外还有 ifneq 关键字，判断是否不相等


## makefile 中使用 shell
```
files := $(shell echo *.c)
```

## 编译 C/C++ 时的隐含规则



