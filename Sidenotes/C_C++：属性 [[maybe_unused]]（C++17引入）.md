**未使用的初始化变量警告**

如果变量已初始化但未使用，现代编译器通常会生成警告（因为这种情况很少见）。如果启用了“将警告视为错误”，这些警告将被升级为错误并导致编译失败。

考虑下面这个看似无辜的程序：

```cpp
int main()
{
    int x { 5 }; // variable x defined

    // but not used anywhere

    return 0;
}
```

当使用 GCC 编译此程序并启用“将警告视为错误”时，会产生以下错误：

```plain
prog.cc：在函数“int main()”中：
prog.cc:3:9：错误：未使用的变量“x”[-Werror=unused-variable]
```

程序无法编译。

有几种简单的方法可以解决这个问题。

1. 如果变量确实未使用且您不需要它，那么最简单的选择是删除它的定义`x`（或将其注释掉）。毕竟，如果它没有被使用，那么删除它不会影响任何事情。
2. 另一种选择是在某处简单地使用变量：

```cpp
#include <iostream>

int main()
{
    int x { 5 };

    std::cout << x; // variable now used somewhere

    return 0;
}
```

但这需要花费一些精力来编写使用它的代码，并且可能会改变程序的行为。

**属性**`[[maybe_unused]](C++17)`

在某些情况下，上述两种选择都不可取。考虑一下我们有一组在许多不同程序中使用的数学/物理值的情况：

```cpp
#include <iostream>

int main()
{
    // Here's some math/physics values that we copy-pasted from elsewhere
    double pi { 3.14159 };
    double gravity { 9.8 };
    double phi { 1.61803 };

    std::cout << pi << '\n';  // pi is used
    std::cout << phi << '\n'; // phi is used

    // The compiler will likely complain about gravity being defined but unused

    return 0;
}
```

如果我们经常使用这些值，我们可能会将它们保存在某个地方并将它们全部复制/粘贴/导入。

但是，在任何我们不使用所有这些值的程序中，编译器可能会抱怨每个实际上未使用的变量。在上面的例子中，我们可以轻松地删除的定义`gravity`。

但如果有 20 或 30 个变量而不是 3 个，该怎么办？

如果我们在多个地方使用它们怎么办？

浏览变量列表以删除/注释掉未使用的变量需要时间和精力。而且，如果我们稍后需要之前删除的变量，我们将不得不花费更多的时间和精力返回并重新添加/取消注释它。

为了解决此类情况，C++17 引入了`[[maybe_unused]]`属性，它允许我们告诉编译器我们可以接受未使用的变量。编译器不会为此类变量生成未使用变量警告。

以下程序不应生成任何警告/错误：

```cpp
#include <iostream>

int main()
{
    [[maybe_unused]] double pi { 3.14159 };  // Don't complain if pi is unused
    [[maybe_unused]] double gravity { 9.8 }; // Don't complain if gravity is unused
    [[maybe_unused]] double phi { 1.61803 }; // Don't complain if phi is unused

    std::cout << pi << '\n';
    std::cout << phi << '\n';

    // The compiler will no longer warn about gravity not being used

    return 0;
}
```

此外，编译器可能会优化程序中的这些变量，因此它们不会影响性能。

该`[[maybe_unused]]`属性应仅选择性地应用于具有特定且合法未使用原因的变量（例如，因为您需要命名值的列表，但在给定程序中实际使用的特定值可能会有所不同）。否则，应从程序中删除未使用的变量。
