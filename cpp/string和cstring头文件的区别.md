# string和cstring头文件的区别



-   \<**string.h**\> 是C语言标准库的头文件之一，包含了一些字符串/内存处理相关的函数（如 strcpy，memcpy 等）。
-   **\<cstring\>** 是C++语言标准库的头文件之一，基本上就是 \<string.h\> 的C++版本，当编写C++程序时如果需要使用 \<string.h\>，则应当用 \<cstring\> 代替，并加上 std:: 前缀（如 std::strcpy，std::memcpy 等）。
-   **\<string\>** 是C++语言标准库的头文件之一，主要包含了 std::basic_string 模板及其相关函数
