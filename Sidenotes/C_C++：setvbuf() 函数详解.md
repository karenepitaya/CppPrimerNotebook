`setvbuf` 是一个 C 标准库函数，用于控制输出流的缓冲行为。它允许你设置流的缓冲方式、缓冲区的大小，以及是否使用自定义缓冲区。这个函数可以用于 `stdin`、`stdout` 或 `stderr` 等标准输入输出流，也可以用于其他文件流。

### **函数原型**
```cpp
int setvbuf(FILE *stream, char *buffer, int mode, size_t size);
```

### **参数说明：**
1. **`stream`**：
    - 这是一个指向 `FILE` 类型的指针，表示需要设置缓冲区的流。常见的值有： 
        * **`stdout`**：标准输出流
        * **`stdin`**：标准输入流
        * **`stderr`**：标准错误流
        * 也可以是其他打开的文件流（通过 `fopen` 等函数获得）。
2. **`buffer`**：
    - 这是一个字符数组指针，指向你希望用于缓冲的自定义缓冲区。如果传入 `NULL`，库函数将自动为该流分配缓冲区。
3. **`mode`**：
    - 缓冲模式，用于指定缓冲区的工作方式。常见的缓冲模式有： 
        * **`_IOFBF`**：全缓冲模式（Fully Buffered）。数据会被缓存在内存中，直到缓冲区满或者程序结束时才会写入目标设备。
        * **`_IOLBF`**：行缓冲模式（Line Buffered）。每当遇到换行符时，缓冲区会自动刷新，将内容写入目标设备。
        * **`_IONBF`**：无缓冲模式（No Buffering）。数据会立即写入设备，不使用任何缓冲区。
4. **`size`**：
    - 指定缓冲区的大小（以字节为单位）。在全缓冲模式和行缓冲模式下，数据会被暂存于这个大小的缓冲区中。无缓冲模式下，这个参数被忽略。

### 返回值：
+ 如果设置成功，`setvbuf` 返回 `0`。
+ 如果出现错误（如无效的流或不允许的缓冲区大小），则返回非零值。

### 缓冲模式详解
1. **全缓冲模式（_IOFBF）**：
    - 在全缓冲模式下，输出会先被缓存在缓冲区中，直到缓冲区满或者程序结束时才会写入目标设备。
    - 这种模式通常用于性能要求较高的应用，能够减少频繁的写入操作。

示例：

```cpp
setvbuf(stdout, nullptr, _IOFBF, 1024); // 设置为全缓冲模式，缓冲区大小为1024字节
```

2. **行缓冲模式（_IOLBF）**：
    - 在行缓冲模式下，输出会被缓存在缓冲区中，每当遇到换行符（`\n`）时，缓冲区会被自动刷新，数据会立即输出到设备。
    - 这通常是终端或交互式程序的默认行为，可以确保每一行的数据会在输出时立即显示。

示例：

```cpp
setvbuf(stdout, nullptr, _IOLBF, 1024); // 设置为行缓冲模式，缓冲区大小为1024字节
```

3. **无缓冲模式（_IONBF）**：
    - 在无缓冲模式下，所有输出都直接写入目标设备，不经过缓冲区。
    - 这适用于需要实时显示输出的场景，比如实时日志记录、进度条等。

示例：

```cpp
setvbuf(stdout, nullptr, _IONBF, 0); // 设置为无缓冲模式，缓冲区大小为0（不使用缓冲）
```

### 使用 `setvbuf` 的注意事项：
1. **调用时机：**
    - `setvbuf` 必须在流的第一次使用之前调用。也就是说，它应该在打开流（如通过 `fopen` 打开文件）后、第一次进行读写操作之前调用。
2. **自定义缓冲区：**
    - 如果你想为流提供一个自定义的缓冲区，可以传递一个指向字符数组的指针。如果不传递自定义缓冲区（即传递 `nullptr`），则库会为你分配一个缓冲区。
    - 注意：当使用自定义缓冲区时，确保这个缓冲区在整个程序中有效，否则会导致未定义的行为。
3. **线程安全：**
    - `setvbuf` 修改的是全局的标准流缓冲区，因此对于多线程程序，需要小心在多个线程中使用。确保适当地同步，以避免竞态条件。
4. **对标准流的影响：**
    - 对 `stdout`、`stdin`、`stderr` 的缓冲区设置，通常会影响整个程序的输出行为。例如，将 `stdout` 设置为无缓冲模式会使所有标准输出的操作都没有缓冲。

### 示例：
以下是一个简单的示例，展示了如何使用 `setvbuf` 来控制 `stdout` 的缓冲模式：

```cpp
#include <iostream>
#include <unistd.h> // 用于 sleep()
using namespace std;

int main() {
    // 设置 stdout 为无缓冲模式
    setvbuf(stdout, nullptr, _IONBF, 0); 
    
    cout << "Hello, world!"; // 立即输出
    sleep(2);                // 程序暂停2秒
    
    cout << " - This is a test."; // 立即输出

    return 0;
}
```

在这个例子中，`stdout` 被设置为无缓冲模式，因此 `cout` 的输出会立即显示，而不会被缓存。

# 按参数介绍
### `buffer` 参数
`buffer` 是一个字符数组，用来指定自定义缓冲区。当我们设置缓冲模式时，我们可以提供一个自定义的缓冲区，而不是让 C 库为我们自动分配。如果不需要自定义缓冲区，可以将 `buffer` 设置为 `nullptr`，并让 C 库为流自动分配缓冲区。

**作用：**

+ 如果你希望自己管理缓冲区，可以传入一个指向字符数组的指针。
+ 如果传入 `nullptr`，系统会自动为流分配缓冲区。

**实例：**

下面的示例演示了如何传递一个自定义的缓冲区。

```cpp
#include <iostream>
#include <cstring>
using namespace std;

int main() {
    char customBuffer[1024];  // 自定义缓冲区
    setvbuf(stdout, customBuffer, _IOFBF, sizeof(customBuffer)); // 设置全缓冲模式并使用自定义缓冲区
    
    cout << "This is using a custom buffer."; // 写入缓冲区
    cout << " You won't see this output immediately."; // 写入缓冲区
    
    return 0;
}
```

#### **代码解析：**
+ `customBuffer` 是一个字符数组，用作输出流的缓冲区。
+ `setvbuf(stdout, customBuffer, _IOFBF, sizeof(customBuffer))` 设置 `stdout` 为全缓冲模式并使用 `customBuffer` 作为缓冲区。
+ 数据会写入 `customBuffer`，并且只有缓冲区满或程序结束时才会被写入目标设备。

### `size` 参数
`size` 参数决定了缓冲区的大小，单位是字节。它告诉程序分配多大的缓冲区。这个值会影响数据写入缓冲区的频率以及缓冲区的使用效率。

**作用：**

+ **全缓冲模式 (`_IOFBF`) 和行缓冲模式 (`_IOLBF`)**：缓冲区的大小会影响缓冲区满的频率。当缓冲区填满时，数据会被刷新到目标设备。
+ **无缓冲模式 (`_IONBF`)**：`size` 参数被忽略，因为在这种模式下，数据不会被缓冲，而是每次都立即写入设备。

**实例：**

我们可以通过不同的缓冲区大小，观察其对输出行为的影响：

```cpp
#include <iostream>
#include <unistd.h>
using namespace std;

int main() {
    // 设置缓冲区大小为 8 字节
    char customBuffer[8];
    setvbuf(stdout, customBuffer, _IOFBF, sizeof(customBuffer));
    
    cout << "This is a long sentence that exceeds the buffer size.";
    sleep(2); // 程序暂停2秒
    return 0;
}
```

**解释：**

+ 设置了一个大小为 16 字节的缓冲区。当输出内容写入缓冲区时，如果缓冲区的内容没有被刷新，程序会等待直到缓冲区满，或者在程序结束时刷新内容到标准输出。
+ 在这个例子中，`customBuffer` 被作为缓冲区传递给 `setvbuf`，并且输出操作会依赖于缓冲区的大小。
+ 如果输出内容比缓冲区的大小小，内容就会存储在缓冲区中，直到缓冲区满时才会被刷新。

**影响：**

+ 如果缓冲区太小，输出操作会更频繁地被刷新，因为每次写入的内容可能填满缓冲区，导致刷新。
+ 如果缓冲区太大，刷新操作会发生得较晚，直到缓冲区填满或程序结束时才刷新。

### `stream`参数
`setvbuf` 函数的作用不仅限于标准输出流（`stdout`），还可以用于其他流，如标准错误流（`stderr`）和文件流。

+ **`stdout`**：标准输出流，通常用于打印信息到终端。
+ **`stderr`**：标准错误流，通常用于打印错误信息，它通常不会被缓冲，意味着它会立即显示出来，即使没有换行符。
+ **文件流**：当你通过 `fopen` 打开文件时，你也可以使用 `setvbuf` 来调整文件流的缓冲策略。

### 示例：使用 `setvbuf` 设置文件流的缓冲
```cpp
#include <iostream>
#include <fstream>
#include <unistd.h>
using namespace std;

int main() {
    // 打开文件以写入
    ofstream outFile("output.txt");
    
    // 设置文件流的缓冲区大小为 64 字节
    char customBuffer[64];
    setvbuf(outFile, customBuffer, _IOFBF, sizeof(customBuffer));
    
    // 向文件中写入内容
    outFile << "Hello, file!";
    sleep(2); // 程序暂停2秒
    outFile << " - This is a test.";
    outFile.close();
    
    return 0;
}
```

**解释：**

+ 在这个例子中，我们通过 `setvbuf` 为 `ofstream`（文件输出流）设置了一个大小为 64 字节的缓冲区。当写入内容时，它会先存储在缓冲区中，直到缓冲区满时才会刷新到文件中，或者文件关闭时才会刷新缓冲区的内容。
+ 通过这种方式，我们可以控制文件流的缓冲行为，提高输出效率。

### 总结：
+ `setvbuf` 是一个用来控制输出流缓冲行为的函数。它可以设置缓冲模式（全缓冲、行缓冲或无缓冲）以及缓冲区的大小。
+ `size` 参数：控制缓冲区的大小。在全缓冲模式和行缓冲模式下，这个参数决定了缓冲区的容量，从而影响缓冲区的刷新频率。在无缓冲模式下，`size` 参数没有影响。
+ **文件流和其他流**：`setvbuf` 不仅能控制 `stdout`，还可以控制 `stderr` 和文件流等其他流。每种流的缓冲行为可能会有所不同，特别是 `stderr` 通常是无缓冲的。
+ 在需要控制缓冲区行为的情况下，使用 `setvbuf` 可以帮助你优化程序的输出行为，尤其是在需要实时显示输出或提高性能时。

