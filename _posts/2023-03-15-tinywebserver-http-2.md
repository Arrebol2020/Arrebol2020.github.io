---
layout: post
title: TinyWebServer 相关函数使用与样例 [http连接处理 下]
date: 2023-03-15 11:38 +0800
categories:
- 项目
- TinyWebServer
tags:
- C++
- http
---



## strpbrk 函数

`strpbrk`函数用于在一个字符串中查找给定字符集合中任意一个字符的第一个匹配位置。

函数声明如下：

```c++
char* strpbrk(const char* str1, const char* str2);
```

函数参数：

- `str1`：指向要在其中搜索的 C 字符串。
- `str2`：指向包含要在 `str1` 中查找的字符集合的 C 字符串。

函数返回值：

- 如果找到一个字符集合中的字符，则返回指向该字符的指针。
- 如果没有找到，则返回空指针。

示例代码：

```c++
#include <iostream>
#include <cstring>

using namespace std;

int main()
{
    char str1[] = "This is a sample string";
    char str2[] = "aeiou";

    char* p = strpbrk(str1, str2);

    if (p != nullptr)
        cout << "First vowel in str1: " << *p << endl;
    else
        cout << "No vowels in str1" << endl;

    return 0;
}
```

输出：

```bash
First vowel in str1: i
```

> 为什么输出 i

因为 str2 的值为 `aeiou`，而 `i`  在 str1 先出现，所以输出为 `i`



## strcasecmp 函数

`strcasecmp` 函数是一个字符串比较函数，用于比较两个字符串是否相等，不考虑大小写。

函数原型如下：

```c++
int strcasecmp(const char* s1, const char* s2);
```

其中 `s1` 和 `s2` 为要比较的两个字符串。

函数返回值为：

- 若 `s1` 和 `s2` 相等，返回值为 `0`。
- 若 `s1` 大于 `s2`，返回值大于 `0`。
- 若 `s1` 小于 `s2`，返回值小于 `0`。

函数的比较规则如下：

- 忽略大小写。
- 以 ASCII 码值为标准进行比较。

示例代码：

```c++
#include <iostream>
#include <cstring>

int main()
{
    const char* s1 = "Hello";
    const char* s2 = "hello";
    int result = strcasecmp(s1, s2);
    std::cout << result << std::endl;
    return 0;
}
```

上述代码中，`s1` 和 `s2` 不相等，但由于忽略大小写，所以函数返回值为 `0`。



## strspn 函数

 `strspn` 函数用于计算字符串中连续字符集合的长度，即返回一个指向 `s` 中第一个不在 `accept` 中出现的字符的指针。

该函数的原型如下：

```c++
size_t strspn(const char* s, const char* accept);
```

其中，`s` 表示要计算的字符串，`accept` 表示要匹配的字符集合。函数返回值是一个 `size_t` 类型，表示字符串中连续字符集合的长度。

函数实现的过程是从 `s` 的开头开始，查找它所包含的字符集合（即 `accept` 中的字符集合），直到找到第一个不在字符集合中的字符为止，返回这个字符之前连续字符集合的长度。

下面是一个简单的示例，演示了如何使用 `strspn` 函数：

```c++
#include <iostream>
#include <cstring>

int main()
{
    const char* s = "Hello, world!";
    const char* accept = "Helo, wrd";
    size_t len = strspn(s, accept);
    std::cout << "The length of the first span in " << s << " is " << len << std::endl;
    return 0;
}
```

上述代码将输出：

```bash
The length of the first span in Hello, world! is 12
```

这说明了 `s` 中前 12 个字符都属于 `accept` 字符集合中的字符。



## strncasecmp 函数

`strncasecmp` 函数用于比较两个字符串的前 n 个字符，忽略大小写。

函数声明如下：

```c++
int strncasecmp(const char* s1, const char* s2, size_t n);
```

参数说明：

- `s1`：要比较的字符串之一。
- `s2`：要比较的字符串之二。
- `n`：要比较的字符数。

返回值：

- 若 s1 的前 n 个字符（不包括结束符 '\0'）字典序小于 s2，则返回负数。
- 若 s1 的前 n 个字符（不包括结束符 '\0'）字典序大于 s2，则返回正数。
- 若 s1 的前 n 个字符（不包括结束符 '\0'）与 s2 相等，则返回 0。

需要注意的是，`strncasecmp` 函数是在 POSIX 标准中定义的，而不是标准的 C++ 函数。因此，在某些操作系统上可能需要定义 `_POSIX_C_SOURCE` 宏或者其他类似的宏来启用这个函数。在 Linux 上，一般在源文件开头加上 `#define _GNU_SOURCE` 即可。



下面是一个使用 `strncasecmp` 函数比较两个字符串忽略大小写的例子：

```c++
#include <iostream>
#include <cstring>

int main() {
    char str1[] = "hello world";
    char str2[] = "Hello World";

    // 比较两个字符串前五个字符忽略大小写是否相同
    int result = strncasecmp(str1, str2, 5);
    if (result == 0) {
        std::cout << "前五个字符相同" << std::endl;
    } else {
        std::cout << "前五个字符不同" << std::endl;
    }

    return 0;
}
```

输出结果为：

```bash
前五个字符相同
```



## stat 函数

`stat` 函数用于获取文件的相关信息，包括文件大小、文件创建时间、文件最后修改时间等。其函数原型如下：

```c++
int stat(const char *path, struct stat *buf);
```

其中，`path` 参数为文件路径，`buf` 参数为用于保存文件信息的结构体指针。

`stat` 函数执行成功时返回 0，执行失败时返回 -1，并将错误码存入 `errno` 变量中。

结构体 `stat`

```c++
struct stat 
{
   mode_t    st_mode;        /* 文件类型和权限 */
   off_t     st_size;        /* 文件大小，字节数*/
};
```



下面是一个示例代码，用于获取文件大小和最后修改时间：

```c++
#include <iostream>
#include <sys/stat.h>
#include <unistd.h>

int main()
{
    const char* filename = "test.txt";
    struct stat st;
    if (stat(filename, &st) != 0)
    {
        std::cerr << "Failed to get file info\n";
        return -1;
    }

    std::cout << "File size: " << st.st_size << " bytes\n";
    std::cout << "Last modified time: " << ctime(&st.st_mtime);
    return 0;
}
```

其中，`ctime` 函数用于将时间戳转换为可读的字符串格式。



## mmap 函数

`mmap()` 函数是一种内存映射文件的方法，可以把一个文件或者一个设备映射到进程的地址空间中，使得进程可以像访问内存一样访问这个文件或者设备。`mmap()` 函数在 Linux 和 Unix 系统下非常常用，可以实现很多高效的文件和数据操作。`mmap()` 函数的原型如下：

```c++
#include <sys/mman.h>
void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
```

参数含义如下：

- `addr`：指定映射区的起始地址，通常设为 `NULL`。
- `length`：指定映射区的长度，单位是字节。
- `prot`：指定映射区的保护方式，可取值为 `PROT_READ`、`PROT_WRITE`、`PROT_EXEC` 和 `PROT_NONE`，分别表示可读、可写、可执行和不可访问。可以使用逻辑或（`|`）将它们组合使用。
- `flags`：指定映射区的类型和各种属性。主要有以下几种取值：
  - `MAP_SHARED`：映射区域与其他进程共享，并且修改是可见的。
  - `MAP_PRIVATE`：创建一个写时复制的私有映射区，映射区的内容对进程是私有的，进程间不可见。
  - `MAP_FIXED`：尝试将映射区域放到指定地址上。如果该地址已被占用或者不符合系统要求，则会导致映射失败。
  - `MAP_ANONYMOUS`：不使用文件，而是创建一个匿名的映射区，映射区的内容被初始化为 0。
- `fd`：指定要映射的文件的文件描述符。如果指定为 `-1`，则表示不使用文件，而是创建一个匿名的映射区。
- `offset`：指定要映射的文件的偏移量，单位是字节。

`mmap()` 函数执行成功后会返回映射区的首地址，通常需要将返回值进行强制类型转换，例如：

```c++
char *addr = static_cast<char *>(mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0));
```

需要注意的是，使用 `mmap()` 函数映射的区域必须使用 `munmap()` 函数进行解除映射，否则会导致内存泄漏。同时，使用 `mmap()` 函数映射的文件必须使用 `close()` 函数关闭，否则会导致文件描述符泄漏。



样例：

```c++
#include <iostream>
#include <fcntl.h>
#include <sys/mman.h>
#include <sys/stat.h>
#include <unistd.h>

int main() {
  const char* filename = "test.txt";
  int fd = open(filename, O_RDONLY);
  if (fd == -1) {
    std::cerr << "Failed to open file " << filename << std::endl;
    return 1;
  }

  struct stat file_stat;
  if (fstat(fd, &file_stat) == -1) {
    std::cerr << "Failed to get file status for " << filename << std::endl;
    close(fd);
    return 1;
  }

  char* file_data = static_cast<char*>(
      mmap(nullptr, file_stat.st_size, PROT_READ, MAP_PRIVATE, fd, 0));
  if (file_data == MAP_FAILED) {
    std::cerr << "Failed to mmap file " << filename << std::endl;
    close(fd);
    return 1;
  }

  // 从 file_data 中读取文件内容
  std::cout << "File content: " << std::endl << file_data << std::endl;

  munmap(file_data, file_stat.st_size);
  close(fd);
  return 0;
}

```

该示例代码打开名为 `test.txt` 的文件，使用 `fstat` 获取文件大小，并使用 `mmap` 将文件映射到内存中。最后，程序从映射的内存中读取文件内容，并在结束前使用 `munmap` 关闭映射。



## iovec 函数

C++中的`iovec`结构体定义在头文件`<sys/uio.h>`中，用于描述一个连续内存块的地址和长度。

`iovec`结构体的定义如下：

```c++
struct iovec {
    void *iov_base; // 内存块地址
    size_t iov_len; // 内存块长度
};
```

`iovec`结构体常用于`readv()`、`writev()`和`recvmsg()`、`sendmsg()`等系统调用中，用于将多个缓冲区的数据读入到一个缓冲区，或将一个缓冲区的数据写入到多个缓冲区。

下面是一个使用`iovec`的示例：

```c++
#include <sys/uio.h>
#include <iostream>
#include <cstring>
#include <unistd.h>

int main()
{
    char buf1[] = "hello ";
    char buf2[] = "world\n";

    struct iovec iov[2];
    iov[0].iov_base = buf1;
    iov[0].iov_len = strlen(buf1);
    iov[1].iov_base = buf2;
    iov[1].iov_len = strlen(buf2);
    ssize_t nwritten = writev(STDOUT_FILENO, iov, 2);
    std::cout << "wrote " << nwritten << " bytes" << std::endl;
    return 0;
}
```

以上示例将两个缓冲区的数据依次输出到标准输出中。首先定义了两个`char`数组，然后定义了一个长度为2的`iovec`数组，其中每个元素都描述了一个缓冲区的地址和长度。最后使用`writev()`将这两个缓冲区的数据依次写入到标准输出中。运行该程序，输出结果为：

```bash
hello world
wrote 12 bytes
```



## writev 函数

`writev()` 函数是 POSIX 标准中定义的一种输入输出函数，用于将多块数据同时写入文件描述符中。该函数的原型如下：

```c++
#include <sys/uio.h>
ssize_t writev(int fd, const struct iovec *iov, int iovcnt);
```

其中，参数 `fd` 表示文件描述符，`iov` 是一个 `iovec` 结构体数组，`iovcnt` 表示 `iov` 数组中元素的个数。

`iovec` 结构体定义如下：

```c++
struct iovec {
    void  *iov_base; // 内存地址
    size_t iov_len;  // 数据长度
};
```

`writev()` 函数会将 `iov` 数组中的多块数据依次写入文件描述符 `fd` 中，从而完成一次写入操作。具体来说，`iov` 数组中每个元素的 `iov_base` 指向数据块的起始地址，`iov_len` 表示数据块的长度。

实例参考 `iovec` 函数



> 循环调用 writev 时，需要重新处理 iovec 中的指针和长度，该函数不会对这两个成员做任何处理。writev 的返回值为已写的字节数，但这个返回值“实用性”并不高，因为参数传入的是 iovec 数组，计量单位是 iovcnt ，而不是字节数，我们仍然需要通过遍历 iovec 来计算新的基址，另外 写入数据的“结束点”可能位于一个 iovec 的中间某个位置，因此需要调整临界 iovec 的 io_base 和 io_len。
> {: .prompt-warning }



## strrchr 函数

`strrchr` 函数用于查找指定字符在一个 C 风格字符串中最后一次出现的位置，如果找到，则返回该位置的指针，否则返回 `NULL`。其函数声明如下：

```c++
char *strrchr(const char *str, int c);
```

其中，`str` 表示待查找的字符串，`c` 表示待查找的字符。

`strrchr` 函数会在 `str` 指向的字符串中从后往前查找字符 `c`，直到找到该字符或者到达字符串的起始位置为止。如果找到了字符 `c`，则返回该位置的指针；否则返回 `NULL`。

下面是一个使用 `strrchr` 函数的示例代码：

```c++
#include <iostream>
#include <cstring>

int main() {
    const char* str = "hello, world!";
    const char* p = strrchr(str, 'o');
    if (p != NULL) {
        std::cout << "The last occurrence of 'o' is at position " << p - str << std::endl;
    } else {
        std::cout << "The character 'o' is not found." << std::endl;
    }
    return 0;
}
```

在上面的代码中，我们首先定义了一个 C 风格字符串 `str`，然后调用 `strrchr` 函数查找字符 `o` 最后一次出现的位置。如果找到了，则打印该位置的下标；否则输出提示信息。



## strncpy 函数

`strncpy` 是 C/C++ 标准库提供的一个字符串拷贝函数，用于将源字符串的一部分拷贝到目标字符串中。其函数原型为：

```c++
char *strncpy(char *dest, const char *src, size_t n);
```

其中 `dest` 为目标字符串的指针，`src` 为源字符串的指针，`n` 表示需要拷贝的字符个数。

如果源字符串的长度小于 `n`，则会将整个源字符串拷贝到目标字符串中，然后将剩余的部分用 `\0` 补齐；如果源字符串的长度大于或等于 `n`，则只会拷贝前 `n` 个字符到目标字符串中。

> 需要注意的是，`strncpy` 不保证拷贝后的目标字符串以 `\0` 结尾，因此我们需要手动在目标字符串的末尾添加一个 `\0`》
> {: .prompt-warning }

另外，如果 `src` 指向的字符串长度小于 `n`，则 `strncpy` 并不会将剩余部分补齐，而是将未拷贝部分也当做字符串拷贝过去，这可能导致目标字符串中存在没有被初始化的字符，因此使用时需要注意。

示例：

```c++
#include <iostream>
#include <cstring>

int main() {
    char src[] = "Hello, world!";
    char dest[20];
    strncpy(dest, src, 5);
    dest[5] = '\0'; // 需要手动添加 NULL 字符，因为 strncpy 不会自动添加

    std::cout << "src: " << src << std::endl;
    std::cout << "dest: " << dest << std::endl;

    return 0;
}
```

输出结果：

```bash
src: Hello, world!
dest: Hello
```



## S_ISDIR(m_file_stat.st_mode)

用于判断 `m_file_stat` 结构体成员 `st_mode` 对应的文件类型是否为目录。

在 Unix 系统中，文件类型信息被存储在 `struct stat` 结构体成员 `st_mode` 中的低12位。其中，文件类型信息占用了低4位，文件访问权限占用了中间的9位，高4位是保留位。通过对 `st_mode` 的位运算，可以得到文件的类型信息和访问权限。



## vsnprintf 函数

`vsnprintf()` 函数是 C++ 标准库中的一个可变参数函数，用于将可变参数格式化成一个字符串。

函数原型如下：

```c++
int vsnprintf(char* str, size_t size, const char* format, va_list ap);
```

函数参数：

- `str`：指向用于存储格式化字符串的字符数组的指针
- `size`：指定存储字符串的字符数组的长度，包括结尾的空字符
- `format`：字符串格式化控制参数
- `ap`：一个指向参数列表的 `va_list` 对象

函数返回值：输出到字符数组 `str` 中的字符数（不包括结尾的空字符），或者如果输出的字符数超过了 `size` 则返回需要写入的字符数，不包括结尾的空字符。

使用 `vsnprintf()` 函数，我们可以将一个格式化的字符串输出到一个字符数组中，具体示例如下：

```c++
#include <cstdio>
#include <cstdarg>

void format_string(char* buffer, size_t size, const char* format, ...)
{
    va_list args;
    va_start(args, format);
    vsnprintf(buffer, size, format, args);
    va_end(args);
}

int main()
{
    char buffer[256];
    format_string(buffer, 256, "%s %d %s", "HTTP/1.1", 200, "OK");
    printf("%s\n", buffer);
    return 0;
}
```

上述代码将 `"HTTP/1.1 200 OK"` 输出到了字符数组 `buffer` 中，并打印输出了该字符数组。注意在调用 `vsnprintf()` 函数之前需要先调用 `va_start()` 函数，之后在函数结束前还需要调用 `va_end()` 函数。



## 参考

- [最新版Web服务器项目详解 - 05 http连接处理（中）](https://mp.weixin.qq.com/s/wAQHU-QZiRt1VACMZZjNlw)
- [最新版Web服务器项目详解 - 06 http连接处理（下）](https://mp.weixin.qq.com/s/451xNaSFHxcxfKlPBV3OCg)



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
