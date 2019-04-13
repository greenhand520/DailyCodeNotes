## 一、CMake是什么

CMake是一个跨平台的安装（编译）工具，可以用简单的语句来描述所有平台的安装(编译过程)。他能够输出各种各样的makefile或者project文件，能测试编译器所支持的C++特性,类似UNIX下的automake。只是 CMake 的组态档取名为 CMakeLists.txt。Cmake 并不直接建构出最终的软件，而是产生标准的建构档（如 Unix 的 Makefile 或 Windows Visual C++ 的 projects/workspaces），然后再依一般的建构方式使用。这使得熟悉某个集成开发环境（IDE）的开发者可以用标准的方式建构他的软件，这种可以使用各平台的原生建构系统的能力是 CMake 和 SCons 等其他类似系统的区别之处。（来自百度百科）

简单说来就是一个跨平台的安装（编译）工具。

## 二、语法

### 1、基本语法

```cmake
cmake_minimum_required(VERSION 3.12)
project(algorithm)

set(CMAKE_CXX_STANDARD 14)

add_executable(algorithm main.cpp)
```

上面是clion新建一个项目只有一个main.c时自动生成的CMakeLists.txt里的内容。

project指令：

定义工程的名称并可指定工程支持的语言，语言可省略，默认支持所有的语言。

> project(project_name [code_lanaguage] )

set指令：

可以用来显式的定义变量。

上面的意思是CMAKE_CXX_STANDARD = 14；

> set(VAR `[VALUE][CACHE TYPE DOCSTRING [FORCE]]`)

比如我们用到的是 SET(SRC_LIST main.c),如果有多个源文件,也可以定义成:

SET(SRC_LIST main.c t1.c t2.c)。

### 2、单目录多源文件的编译

假如你的项目中只有下面4个源文件main.cpp、mod.hpp、mod_func1.cpp、mod_func2.cpp。因为cmake可以很轻松地解析出各文件的依赖关系，因此CMakeLists.txt其实十分简单：

```cmake
cmake_minimum_required(VERSION 2.8)
add_executable(Main
  main.cpp
  mod_func1.cpp
  mod_func2.cpp
)
```

### 3、多目录程序的编译

假如你的项目的文件结构如下：

```
项目名/
  main.cpp
  mod1.hpp
  mod1/
    func1.cpp
    func2.cpp
  mod2.hpp
  mod2/
    func1.cpp
    func2.cpp
```

一般有以下两种方法：

#### 1）、整个项目仅编写单个CMakeLists.txt

在项目的根目录下编写CMakeLists.txt：

```cmake
cmake_minimum_required(VERSION 2.8)
add_executable(Main
  main.cpp
  mod1/func1.cpp
  mod1/func2.cpp
  mod2/func1.cpp
  mod2/func2.cpp
)
```

#### 2）、每个目录均编写一个CMakeLists.txt

推荐使用这种方法，虽然它看似要编写的代码会增多，但由于更加模块化，管理起来会更加轻松。

```cmake
#CMakeLists.txt
cmake_minimum_required(VERSION 2.8)
add_subdirectory(mod1) 
add_subdirectory(mod2) 
add_executable(Main main.cpp)
target_link_libraries(Main Mod1 Mod2) 
```

```cmake
#mod1/CMakeLists.txt
cmake_minimum_required(VERSION 2.8)
add_library(Mod1 STATIC
  func1.cpp
  func2.cpp
)
```

```cmake
#mod2/CMakeLists.txt
cmake_minimum_required(VERSION 2.8)
add_library(Mod2 STATIC
  func1.cpp
  func2.cpp
)
```

