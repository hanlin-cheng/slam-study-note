# GoogleTest

## GoogleTest运行特定的测试用例

通常GoogleTest的单元测试，直接执行，就全跑一遍，很耗时，有时候需要只测试某个case。

先列出所有case：

```
D:\my_workspace\build\Debug>hello_test.exe --gtest_list_tests
Running main() from D:\my_workspace\build\_deps\googletest-src\googletest\src\gtest_main.cc
HelloTest.
  MyTest
  MyTest2
FactorialTest.
  Negative
  Zero
  Positive
IsPrimeTest.
  Negative
  Trivial
```

然后执行特定case：

```
hello_test.exe --gtest_filter="HelloTest*"
```

或者：

```
hello_test.exe --gtest_filter="HelloTest.MyTest2"
```

如果你的测试程序加这些后缀无效，可能是你的main函数写错了，把main函数删除，GoogleTest默认有main函数的，可以编译过。