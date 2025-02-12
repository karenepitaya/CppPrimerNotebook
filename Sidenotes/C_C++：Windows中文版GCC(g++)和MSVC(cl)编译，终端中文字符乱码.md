> **问题描述：使用**`**g++**`**或者**`**cl**`**编译时控制台输出的中文字符乱码**
>
> **原      因：Windows中文版终端控制台字符编码集为**`**gb2313**`**，源码字符编码集为**`**UTF-8**`
>
> **解决办法：通过编译命令选项指定源文件编码为**`**UTF-8**`**，而程序运行时使用的编码集为系统的编码也就是**`**gb2313**`**，这样做的好处是既不用改源代码，又不用调整终端编码格式，使得在最小的修改范围内解决乱码问题**
>

---

```powershell
g++ -o version .\version.cpp -finput-charset=UTF-8 -fexec-charset=GBK
```

+ `-finput-charset=UTF-8`：指定源文件的编码为 UTF-8。
+ `-fexec-charset=GBK`：指定程序运行时的字符串编码为 GBK，确保与 Windows 默认的本地编码一致，从而避免控制台显示乱码。

```powershell
cl /source-charset:utf-8 /execution-charset:gb2312 /EHsc version.cpp
```

+ `/source-charset:utf-8`：处理源文件的编码。
+ `/execution-charset:gb2312`：确保程序输出的字符串编码与系统本地一致。
+ `/EHsc`：启用标准 C++ 异常处理语义，推荐在所有现代 C++ 项目中使用。  

