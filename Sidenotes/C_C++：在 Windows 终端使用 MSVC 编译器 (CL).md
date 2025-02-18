>  MSVC（Microsoft Visual C++）是微软的 C++ 编译器和开发工具，属于 Microsoft Visual Studio（VS）的一部分。它是专为 C++ 程序设计的编译器，提供了 C++ 编译、调试、链接和其他开发工具。理解 MSVC 与 C++ 的关系，首先需要理解编译器、标准库和开发环境的基本概念。  
>

### 1. **C++编程语言**
C++ 是一种面向对象的编程语言，广泛用于系统软件、游戏开发、嵌入式系统等领域。C++ 提供了高级的抽象功能，也允许开发人员直接操作硬件，因此它非常适合需要高性能的应用程序。

### 2. **编译器（Compiler）**
编译器是将 C++ 源代码（.cpp 文件）转换成计算机能够执行的机器代码（.exe、.obj 文件等）的工具。编译器的主要任务包括：

+ 词法分析和语法分析：将源代码转换成计算机可以理解的中间表示。
+ 优化：提高代码的执行效率。
+ 代码生成：生成目标机器的机器码。

**MSVC 就是微软的 C++ 编译器**，它将 C++ 源代码编译成 Windows 可执行文件（.exe、.dll 等）。

### 3. **MSVC 编译器与 C++ 标准库**
编译器本身负责将 C++ 代码翻译成机器代码，但标准库提供了大量常用的代码功能，如输入输出流、容器类（如 `std::vector` 和 `std::map`）、数学运算等。

**C++ 标准库** 是 C++ 编程语言的一部分，提供了大量的通用函数和数据结构。而 MSVC 的 C++ 编译器会根据不同的编译选项（如链接不同的库）来选择使用不同版本的标准库。常见的标准库包括：

+ **静态运行时库（Static Runtime Library）**：将所有的 C++ 标准库代码与应用程序一起编译，生成一个大而独立的可执行文件。
+ **动态运行时库（Dynamic Runtime Library）**：将标准库代码与操作系统的动态链接库（DLL）一起打包，这样应用程序运行时可以共享这些库文件。

这两者的选择会影响到程序的大小、性能和依赖性。

### 4. **MSVC 的 C++ 运行时库**
MSVC 提供了一些运行时库（Runtime Libraries），这些库包含了用于执行 C++ 程序所需的基本功能。例如，`LIBCMT.lib` 就是其中一个常见的库，它提供了静态链接的多线程运行时支持。不同的运行时库可以影响程序的编译和执行方式。

+ **LIBCMT.lib**：静态多线程运行时库，它通常用于静态链接（/MT）配置。
+ **MSVCRT.dll**：动态链接的 C 运行时库，通常用于动态链接（/MD）配置。

根据你在项目中选择的运行时选项，链接器会选择相应的库文件。例如，使用 `/MT` 时，链接器会选择 `LIBCMT.lib`；使用 `/MD` 时，链接器则会使用 `MSVCRT.lib`。

### 5. **链接（Linking）过程**
编译器将源代码编译为目标文件（.obj 文件），链接器将多个目标文件和库文件（如标准库）合并成一个可执行文件。在链接时，如果项目配置使用了静态运行时库，链接器就会尝试链接 `LIBCMT.lib`。如果找不到这个库文件，就会报错（如你遇到的 `LNK1104: cannot open file 'LIBCMT.lib'`）。

解决方法通常是确保：

+ 项目的编译选项正确（如 `/MT` 或 `/MD`）。
+ 链接器能够找到所需的库文件。

### 在命令行中使用MSVC(cl)编译器
+ 将`bin`目录加入环境变量

上官网下载最新版本的MicroSoft Visual Studio后可以在`C:\Program Files (x86)\Microsoft Visual Studio\<version>\Community\VC\Tools\MSVC\<version>\bin\Hostx64\x64`里找到`cl.exe`和`link.exe`，将这个目录放到`PATH`环境变量中

+ 配置`INCLUDE`和`LIB`环境变量

这一步是为了避免两个报错，参见[使用msvc的cl工具编译程序，以及 “fatal error C1034: iostream: 不包括路径集”等问题解决-CSDN博客](https://blog.csdn.net/weixin_41115751/article/details/89817123)

```bash
C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.42.34433\include
C:\Program Files (x86)\Windows Kits\10\Include\10.0.22000.0\winrt
C:\Program Files (x86)\Windows Kits\10\Include\10.0.22000.0\um
C:\Program Files (x86)\Windows Kits\10\Include\10.0.22000.0\ucrt
C:\Program Files (x86)\Windows Kits\10\Include\10.0.22000.0\shared
C:\Program Files (x86)\Windows Kits\10\Include\10.0.22000.0\cppwinrt\winrt
```

```bash
C:\Program Files (x86)\Windows Kits\10\Lib\10.0.22000.0\um\x64
C:\Program Files (x86)\Windows Kits\10\Lib\10.0.22000.0\ucrt_enclave\x64
C:\Program Files (x86)\Windows Kits\10\Lib\10.0.22000.0\ucrt\x64
C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.42.34433\lib\x64
```

接下来就可与愉快的编译了：

```powershell
cl /source-charset:utf-8 /execution-charset:gb2312 /EHsc version.cpp
```

+ `/source-charset:utf-8`：处理源文件的编码。
+ `/execution-charset:gb2312`：确保程序输出的字符串编码与系统本地一致。
+ `/EHsc`：启用标准 C++ 异常处理语义，推荐在所有现代 C++ 项目中使用。  



