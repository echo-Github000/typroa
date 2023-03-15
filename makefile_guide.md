#### makefile guide

![图片](makefile.png)

##### 常用的自动化变量有三个，为$@,$<,$^



```markdown
main:main.o file1.o file2.o
    gcc -o $@ $^
    //gcc -o main main.o file1.o file2.o

main.o:main.c
    gcc -c $<
    //gcc -c main.c
    //gcc -c main.c -o main.o

file1.o:file1.c
    gcc -c $<
    //gcc -c file.c

file2.o:file2.c
    gcc -c $<
    //gcc -c file2.c
```

```markdown
main:main.o file1.o file2.o
    gcc -o $@ $^
    //gcc -o main main.o file1.o file2.o

%.o:%.c
    gcc -c $<
```

```markdown
main:main.o file1.o file2.o
    gcc -o $@ $^
    //gcc -o main main.o file1.o file2.o

.c.o:
    gcc -c $<
```

#### gcc 的编译选项详解

编译过程：

​	预处理（-E）：将.c 、.h、头文件、库文件、宏定义等展开成一个文件，即为.i文件

​	汇编(-S)：将.i文件生成一个汇编文件，即为.S文件

​	编译(-c)：将.S文件编译成目标文件，即为.o文件，也称.obj文件

​	链接(-o)：将目标文件链接成一个可执行文件，即为.exe(win)或.elf(linux)文件



1. <-o> : 指定输出目标文件的名称，如```gcc main.c -o main```，执行完后会生成main.exe的可执行文件

#### makefile 规则

1. 变量：

   =（替换），+=（追加），:=（常量）

   例如：

   ```markdown
   TAR = main
   OBJ = main.o
   CC := gcc
   #那么下面
   main:main.o
   	gcc -c main.c
   
   #就可以替换为：
   $(TAR):$(OBJ)
   	$(CC) -c main.c
   
   #再通过自动化变量就可以简化为如下：
   $(TAR):$(OBJ)
   	$(CC) -c $<
   ```

   使用变量：$(变量)

2. 隐含规则：```%.c， %.o```，```.c.o```

   任意的.c 和 .o文件

3. 自动化变量：

   $@:目标文件

   $^:所有的依赖文件

   $<:依赖文件的第一个文件