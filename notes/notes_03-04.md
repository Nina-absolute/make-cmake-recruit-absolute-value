
---
---
# Task 3 
---
## #1 分析命令中的陌生概念

1. `cmake_minimum_required` 指定最低 CMake 版本

声明需要的最低版本，确保 `CMake` 命令兼容性，版本过低会报错。
例：`cmake_minimum_required(VERSION 3.16)`

2. `project`
用于定义项目名和使用的语言，设置项目基本信息，影响后续变量和构建规则。

- 规范：`project(项目名 LANGUAGES 语言)`
- 示例：`project(calculator LANGUAGES C)`

3. `add_executable` = 添加可执行目标

- **指定源文件**，生成可执行文件。
- 规范：`add_executable(目标名 源文件1 源文件2 ...)`

4. `target_include_directories` = 指定目标头文件搜索路径

告诉编译器去哪里找头文件。为目标添加头文件搜索路径，作用范围由 `PRIVATE`、`PUBLIC`、`INTERFACE` 控制。
- 规范：`target_include_directories(目标名 PRIVATE 路径)`
- 示例：`target_include_directories(calculator PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)`
*这么写的好处是：只要固定头文件和 CMakeLists 文件的相对位置，就能一直保持动态正确*

```text
  cmake-task/
  ├── CMakeLists.txt
  ├── include/
  │   └── calculator.h
  └── src/
    ├── main.c
    └── calculator.c
```
这么写大概是某种惯例，而且正常人一般都不会把要用的头文件和源码目录隔开十万八千里远吧，所以说放心地写`${CMAKE_CURRENT_SOURCE_DIR}/include`就完了

ps:之前说 CMakeLists 文件一定要在指定源码目录里或者根目录，头文件也应当这样，要是在 CMakeLists 文件上级，虽然查了一下`${CMAKE_CURRENT_SOURCE_DIR}/../include`也是可以工作的，但总而言之项目结构肯定怪混乱的。🙅‍


5. `PRIVATE` = 限定头文件路径只影响当前目标（不污染其他文件）

`CMake` 作用域关键字，表示该路径值传给一个目标文件，不传递给依赖当前目标的其他目标（依赖方）。
- `PUBLIC`传递给当前目标，也传递给其他依赖方
- `INTERFACE`只传递给其他依赖方

### **一些些不成熟的思考**:为什么要区分这个参数？
简单来说，这或许是在针对不同目标文件和不同依赖方，基于用途，进行某种权限划分。
比如说，这一部分只希望内部可见，就用PRIVATE；那一部分可以完全开放，就用PUBLIC。
#### 【疑问待解决】
但是仍然没太懂这些在现实开发中的具体应用场景是什么。



6. ==`${CMAKE_CURRENT_SOURCE_DIR}`==

= 当前源码目录变量，写在 CMakeLists 文件里就会*自动*展开为这个文件所在的*绝对路径*

---
## #2 关于陌生参数

1. `-S` Source
指定 `CMakeLists.txt` 所在目录，源码目录。

2. `-B` build
指定生成构建文件的目录。
`cmake -S . -B build` = 构建文件放进 `build/`。

*注意：用`cmake`时一定要写类似`-S . -B build`的语句，因为不写就都默认当前目录，会污染源码。*


3. `build` 构建目录名
存放 `CMake` 生成的构建系统的文件夹，避免污染源码目录。

4. `--build`构建选项
告诉`cmake`开始构建。
---
---

# Task 4 
## 此处贴一个查 ai 的拓展

1. `Make` 相比 `build.sh` 的优势：

- **增量构建**：只重新编译修改过的文件，不重复劳动。
- **依赖管理**：显式记录目标与依赖的关系，头文件改了会触发对应 `.o` 重新编译。
- **自动判断**：通过时间戳自动决定是否执行命令。
- **并行构建**：支持 `-j` （`make` 的命令行选项，指定并行任务数量）参数并行构建。
- **可维护性**：声明式描述构建规则，逻辑清晰。
- **可扩展性**：支持变量、自动变量、模式规则、函数。

2. 全量重编译会带来：

- **时间成本**：50 个文件全量编译可能需要几分钟甚至更久，增量构建可能只需要几秒钟。
- **资源消耗**：全量编译占用大量 CPU、内存、磁盘 I/O（`Disk Input/Output`, CPU 与磁盘之间的数据读写，包括读文件和写文件）。
- **开发效率低**：修改一行代码后等待全量编译，打断开发节奏。
- **CI/CD 成本高**：持续集成（ Continuous Integration ）中，全量构建增加服务器负担和等待时间。
  - `CI`：开发人员频繁地把代码合并到主干，每次合并都自动构建、自动测试，尽早发现集成错误。
  - `CD`（ Continuous Delivery ）：在 `CI` 基础上，自动把通过测试的代码交付到测试环境或生产环境。

- **大型项目不现实**：大型项目可能上千个 `.c` 文件，全量编译不可行。
