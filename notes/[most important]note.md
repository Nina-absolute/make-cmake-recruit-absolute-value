# Make & CMake
---
---
# Tsak 0 Preparation 阅读题前文字
## #1 必须知道的概念汇总
1. `-Iinclude`

- `-I`指定头文件路径 = include directory, 告诉预处理器去哪里找头文件
---
### ==2. 关于 Makefile ==

核心结构 be like:
>```makefile
>target: prerequisites
>      recipe
>```
> - 我要得到什么？
> - 它依赖什么？
> - 需要执行什么命令得到它？

1. `target`目标（我要得到什么？）
= 要生成的文件或者动作名

2. `prerequisites`先决条件（它依赖什么？）
= 目标依赖的文件

3. `recipe`命令（需要执行什么命令得到它？）
= 生成目标需要的命令

==为什么要有 Makefile ?==
如果把“我要得到什么、依赖什么、怎么得到”这些写成规则，让 `Make` 自动判断哪些步骤需要执行。那么就不用自己敲编译命令/背哪些文件还没有编译了。

---
### 3. 关于 C
1.  CC = C compiler = C 编译器变量，存放编译器名，方便替换编译器（gcc -> 实际的编译器文件，还是有点类似于常量展开）

2. CFLAGS = C flags = C 编译选项变量，存放编译选项，相当于编译器的设置（？

3. `$@` = 要生成的文件，也即前文提到的`target`

*注意：虽然和 Shell 里的那个长得一摸一样，但是完全不是一个意思。*

常用`-o $@`，如果不加`-o`的话就没办法指定名称了。

4. `$<` = Dollar less = 第一个 `prerequisites`
常用于指定第一个依赖的文件，也就是说指定编译第一个文件。

那为什么不直接写文件名呢？可能是因为太长了。
其他位置的依赖文件就不能被指定了吗？

5. `$^` = Dollar Caret = 所有依赖。

6. 增量构建
-> 本质上是`Make`通过比较目标和依赖的修改时间，实现对最近修改的文件的构建。


---
### 4. 关于 CMake

- `CMake` — `Cross-platform Make` — 跨平台构建系统生成器 — 读取 `CMakeLists.txt` 生成 `Makefile` 或 `Ninja` 文件 — 像“建筑图纸生成器” — `cmake -S . -B build` — 生成构建系统。
- `CMakeLists.txt` — `CMake Lists Text` — CMake 配置文件 — 描述项目、目标、头文件路径 — 像“建筑图纸要求” — `cmake -S . -B build` — 作为 CMake 输入。
- `cmake_minimum_required` — `CMake Minimum Required` — 指定最低 CMake 版本 — 确保命令兼容 — 像“最低配置要求” — `cmake_minimum_required(VERSION 3.16)` — 声明版本要求。
- `project` — `Project` — 定义项目名和语言 — 设置项目基本信息 — 像给工程起名字 — `project(calculator LANGUAGES C)` — 定义项目。
- `add_executable` — `Add Executable` — 添加可执行目标 — 指定源文件生成可执行文件 — 像“要组装哪辆车” — `add_executable(calculator src/main.c src/calculator.c)` — 定义可执行目标。
- `target_include_directories` — `Target Include Directories` — 指定目标头文件搜索路径 — 告诉编译器去哪里找头文件 — 像告诉图书馆管理员去哪个书架 — `target_include_directories(calculator PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)` — 解决头文件路径。
- `PRIVATE` — `Private` — 私有作用域 — 只影响当前目标 — 像“只给这辆车用” — `PRIVATE` — 限定头文件路径作用范围。
- `${CMAKE_CURRENT_SOURCE_DIR}` — `CMake Current Source Directory` — 当前源码目录变量 — 展开为当前 `CMakeLists.txt` 所在目录 — 像“当前图纸所在文件夹” — `${CMAKE_CURRENT_SOURCE_DIR}/include` — 构造头文件绝对路径。

### 核心命令参数详解

- `cmake` — `CMake` — 构建系统生成器 — 读取 `CMakeLists.txt` 生成构建系统 — 像“图纸生成器” — `cmake -S . -B build` — 生成构建系统。
- `-S` — `Source` — 源码目录 — 指定 `CMakeLists.txt` 所在目录 — 像“图纸在哪里” — `cmake -S . -B build` — 指定源码目录为当前目录。
- `.` — `Current directory` — 当前目录 — 表示当前工作目录 — 像“就在这里” — `cmake -S . -B build` — 当前目录作为源码目录。
- `-B` — `Build` — 构建目录 — 指定生成构建文件的目录 — 像“施工文件放哪里” — `cmake -S . -B build` — 构建文件放进 `build/`。
- `build` — `Build` — 构建目录名 — 存放 CMake 生成的构建系统 — 像“施工文件夹” — `cmake -S . -B build` — 避免污染源码目录。
- `cmake --build build` — `CMake Build` — 执行构建 — 调用生成的构建系统完成编译 — 像“按图纸施工” — `cmake --build build` — 在 `build/` 中构建。
- `--build` — `Build` — 构建选项 — 告诉 `cmake` 执行构建而不是生成 — 像“开始施工” — `cmake --build build` — 执行构建。
- `./build/calculator` — `Build Calculator` — 运行构建出的程序 — 执行 `build/` 下的 `calculator` — 像“开走造好的车” — `./build/calculator` — 运行程序。
---
---

# Task 1 编译流程笔记
## #1 必须知道的概念大整合

### #1 关于命令

1. `gcc`
= GNU Compiler Collection, GNU编译器集合

### #2 关于文件类型
1. `.c`
表示用 C 语言写的源文件

2. `.i`
= preprocessed
表示预处理后的文件

3. `.s`
= assembly
编译得到的汇编文件

4. `.o`
= object
汇编代码变机器码生成的目标文件

### #3 关于参数
1. `-E`
= Preprocess only, 只做预处理

2. `-o`
= output, 指定输出文件（也就是说，指定输出文件的名称和类型）

3. `-S`
= stop after compilation, 编译后停止，生成汇编文件

4. `-c`
= complie only, 只编译不链接

*疑问：如果不加这些参数会怎么样？
*那么`gcc`不会停在中间步骤，而是继续下去，直到生成可执行文件*

### #4 ==关于一些不熟悉的指令==
\\非常非常不熟的要重点理解的加了`*`标注

1. `#include`
让预处理器把另一个文件的内容插入到当前文件位置

2. *`#define` 定义宏 
把宏名替换为指定文本

*关于宏（macro）
预处理器执行的文本替换规则，分为对象宏和函数宏；
- 对象宏：不带参数的宏（类似普通的常量替换）
- 函数宏：带参数的宏（类似替换了一段带参数的代码片段）


## #2 编译流程笔记（详细版）

### 1. 预处理（`.c`->`.i`）
// `gcc -E hello.c -o hello.i`

预处理`.c`源文件时：
  - 展开 `#include`，把头文件内容插入当前文件
  - 处理 `#define`、`#ifdef`、`#ifndef`、`#endif` 等，简单来说就是依据某种规则（宏展开，宏是否定义，条件是否为真等）对代码进行删改替换，类似于常量替换。
  - 删除注释，专注于纯粹的有效的命令行。
  - 行拼接，把换行后的语句*在逻辑上*连成一行
  - 行控制，在预处理输出中补充`#line`，大概是一种证明有理有据的办法，便于编译错误时定位源文件。

**预处理 = 处理掉`#include`等等，把源文件 -> 纯 C 代码文件。**

### 2. 编译（`.i`->`.s`）
// `gcc -S hello.i -o hello.s`

编译预处理文件时：
// 使用“专业名词 + 个人大白话解释”格式分析流程

阶段一：检查

  - 词法分析：把一条命令行拆成基本的分析单元（token）；
  - 语法分析：检查命令行的格式对不对（此处即可产生语法报错，比如缺`;`什么的）
  - 语义分析：检查命令行的意思对不对（此处可以产生报错，比如未声明变量）

阶段二：优化

  - *中间表示生成：大概相当于打了一份编译后汇编文件的草稿（？，比较接近，但是并不是汇编的形式。
  - 优化：不改变程序的前提下，优化编译，用于提高运行速度、减小文件体积。
  （优化的过程出于行文简洁的考虑，只在附加的学习笔记文件里贴出常见优化，此处略过。）
  - 目标代码生成：中间表示变汇编代码，草稿变正稿。

**编译 = 经过检查和优化，把纯 C 代码 -> 接近机器语言的汇编文件。**

### 3. 汇编（`.s` -> `.o`）
// `gcc -c hello.s -o hello.o`

汇编文件变为机器码时：
  - 读取汇编文件，翻译为机器码：通过汇编器，汇编代码翻译为机器码；
  - 生成节区：翻译后的目标文件分类放好；
  - 生成符号表：写出函数名/变量名对照关系（代号-> 具体内容），类似常量展开；
  - *生成重定位表：告诉链接器哪里需要改地址，便于修正目标文件中地址引用。
  - 输出目标文件。

**汇编 = 把汇编代码转换成机器码，生成目标文件。**

### 4. 链接（`.o`->可执行文件）
// `gcc hello.o -o hello`

解析 `printf` 等外部符号，合并节区，根据重定位表修正地址引用，加入启动代码并设置入口点，最后生成可执行文件 `hello`。

**链接 = 把多个目标文件以及需要的库组合起来，最终生成可执行程序。**


## #3 关于编译失败 vs 链接失败
- 编译失败：发生在编译阶段，通常是语法错误、类型错误。
- 链接失败：发生在链接阶段，通常是函数未定义、库缺失。

---

# Task 2 完成 Makefile

## #1 分析命令中的陌生概念

1. 
- `CFLAGS`：`C Flags`，C 编译选项。
// 统一管理编译选项，避免每条命令重复写 `-Wall -Wextra -Iinclude`。

2. 
- `-Wall`：`Warnings all`，开启常用警告。
- `-Wextra`：`Warnings extra`，开启额外警告。
// 让编译器多报警告，提前发现潜在 bug

3. 
- `-Iinclude`：`Include directory`，指定头文件搜索路径为 `include`。
// 告诉预处理器去 `include/` 找 `calculator.h` 和 `logger.h`，否则会报 `No such file or directory`。

4. 
`.PHONY`= Phony target 伪目标
- 告诉 `Make` 该目标不是真实文件，总是执行 `recipe`

具体示例的详细说明：
Makefile 文件中，
对于
```makefile
clean:
	rm -f calculator *.o
```
会认为`clean`是一个要生成的文件，所以后续执行时，会出现两种情况：
- 一种是恰有一个叫`clean`的文件，那么永远也不会执行`clean`命令了；
- 如果没有的话`Make`会检查 `clean` 目标，发现它不是一个真实存在的文件，于是无条件执行对应的`recipe`也就是我们想让它执行的命令，只能说歪打正着🤣。
所以严格意义上需要`.PHONY: clean`来规避。

5. `touch` 
修改文件时间戳 = 更新文件的访问和修改时间为当前时间，文件不存在则创建空文件。
-> 本质上是通过修改时间戳，让 Make 重新注意到它并且再次编译。
示例：
`touch src/calculator.c` 触发 `Make` 重新编译该文件。


