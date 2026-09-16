cmake_minimum_required(VERSION 3.16)

project(calculator LANGUAGES C)

# TODO: create an executable target named calculator
# Hint: sources are in src/main.c and src/calculator.c
add_executable(calculator
    src/main.c
    src/calculator.c
)
# 增加了可执行目标和对应的源文件

# TODO: add the include directory for the calculator target
target_include_directories(calculator
    PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)
# 为 calculator 目标添加了 include 头文件搜索路径