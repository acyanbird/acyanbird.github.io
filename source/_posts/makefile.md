---
title: makefile
date: 2021-11-21 17:19:53
categories:
tags:
- c语言
---

自动编译用，可以直接使用 make 来生成文件，不过学校的版本实在是有点太……封装了，我们从头了解一下，说不定 h 的错误就在这里呢

```makefile
PRODUCT := idsniff
BUILDDIR := ../build

HDRS := $(wildcard ./*.h)
SRCS := $(wildcard ./*.c)
BINARY := $(BUILDDIR)/$(PRODUCT)
OBJS := $(SRCS:./%.c=$(BUILDDIR)/%.o)

CC:=gcc

CFLAGS := -g -DDEBUG -Wall
LDFLAGS := -lpthread -lpcap

.PHONY: all clean	# .PHONY 代表后面的 all clean 不是文件

all: $(BINARY)

clean:
	rm -rf $(BUILDDIR)

$(BINARY): $(OBJS)
	echo $(OBJS)
	@echo linking $@
	$(maketargetdir)
	$(CC) -o $@ $^ $(LDFLAGS)

$(BUILDDIR)/%.o : ./%.c
	@echo compiling $<
	$(maketargetdir)
	$(CC) $(CFLAGS) $(CINCLUDES) -c -o $@ $<

define maketargetdir
	-@mkdir -p $(dir $@) > /dev/null 2>&1
endef
```



让我们从头开始

https://seisman.github.io/how-to-write-makefile/introduction.html

#### makefile的规则

```
target ... : prerequisites ...
    command
```

- target

  可以是一个object file（目标文件），也可以是一个执行文件，还可以是一个标签（label）。对于标签这种特性，在后续的“伪目标”章节中会有叙述。

- prerequisites

  生成该target所依赖的文件和/或target

- command

  该target要执行的命令（任意的shell命令）

这是一个文件的依赖关系，也就是说，target这一个或多个的目标文件依赖于prerequisites中的文件，其生成规则定义在command中。说白一点就是说:

```
prerequisites中如果有一个以上的文件比target文件要新的话，command所定义的命令就会被执行。
```



那么范例文件可以看

```makefile
edit : main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o
    cc -o edit main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o
        #  \ 是为了更加容易解读，没意义，cc 和 gcc 类似

main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o

```

在这个makefile中，目标文件（target）包含：执行文件edit和中间目标文件（ `*.o` ），依赖文件（prerequisites）就是冒号后面的那些 `.c` 文件和 `.h` 文件。`clean` 不是一个文件，它只不过是一个动作名字。



#### makefile 是怎么工作的

