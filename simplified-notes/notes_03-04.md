# Make & CMake：Task 3–4 笔记

## Task 3：用 CMake 描述目标

`CMake` 是构建系统生成器，不是编译器。它读取 `CMakeLists.txt`，根据所选生成器生成 Ninja、Make 或 IDE 的构建规则；规则再调用配置好的 C 编译器。

```cmake
cmake_minimum_required(VERSION 3.16)
project(calculator LANGUAGES C)

add_executable(calculator
  src/main.c
  src/calculator.c
)

target_include_directories(calculator PRIVATE
  ${CMAKE_CURRENT_SOURCE_DIR}/include
)
```

- `cmake_minimum_required` 声明所需的最低 CMake 版本。
- `project` 定义项目及使用的语言。
- `add_executable` 创建可执行目标并列出其源文件。
- `target_include_directories` 为指定目标添加头文件搜索路径。

`PRIVATE` 表示该 include 路径只供 `calculator` 自身编译使用；`PUBLIC` 还会传递给依赖该目标的目标；`INTERFACE` 只传递给依赖方。这是“使用需求”的传播范围，不是文件访问权限。

```bash
cmake -S . -B build
cmake --build build
./build/calculator
```

`-S` 指定源码目录，`-B` 指定构建目录。将构建产物放在 `build/` 是推荐的 out-of-source build 做法，便于清理并保持源码目录整洁，但不是 CMake 的唯一调用方式。`${CMAKE_CURRENT_SOURCE_DIR}` 是当前正在处理的源码目录的绝对路径，因此可稳定定位项目内的 `include/`。

## Task 4：构建工具的分工

- 简单的 `build.sh` 通常无条件顺序执行命令；Make 通过依赖关系和时间戳实现增量构建，并支持并行构建。
- CMake 负责生成或驱动构建系统；`cmake --build build` 使用生成器后端（例如 Make 或 Ninja）执行构建，最终由该构建配置选择的编译器（本题 Linux 环境中通常是 GCC）编译 C 文件。
- 只改一个 `.c` 文件时，全量重编译会浪费时间和 CPU、内存、磁盘 I/O；正确声明依赖后，构建系统只重建受影响的目标。
