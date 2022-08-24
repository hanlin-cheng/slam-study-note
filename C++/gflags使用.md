# gflags使用

## 简单用法

### 1.加上 `gflags` 头文件

```
#include <gflags/gflags.h>
```

### 2.将需要命令行参数变量进行定义

用法如下：

```
DEFINE_string(变量名，默认值，描述);
```

`DEFINE_string` 只是其中一种类型，其他类型还有：

| gflags 定义类型 | 描述             |
| --------------- | ---------------- |
| DEFINE_bool     | 布尔类型         |
| DEFINE_int32    | 32 位整数        |
| EFINE_int64     | 64 位整数        |
| DEFINE_uint64   | 无符号 64 位整数 |
| EFINE_double    | 浮点类型 double  |
| DEFINE_string   | C++ string 类型  |

### 3.在main函数加入如下解析命令行参数，一般都放在 main 开始位置

```
gflags::ParseCommandLineFlags(&argc, &argv, true);
```

### 4.访问参数

```
std::cout << "MYSTR is: " << FLAGS_mystr << endl;
```

**注意使用`FLAGS_` + `DEFINE_string` 中申明的变量名即可调用**

### 5.使用

```
g++ gflags_test.cc -o gflags_test -lgflags -lpthread 		#-l 链接库进行编译
./gflags_test -mystr="this is a value of gflags' member "	# 使用
MYSTR is: this is a value of gflags' member  				# 输出
```


每一种类型的定义和使用都跟上面我们的例子相似，有所不同的是 bool 参数，bool 参数在命令行可以不指定值也可以指定值，假如我们定义了一个 bool 参数 debug_bool，可以在命令行这样指定2：

```
./gflags_test -debug_bool 		# 这样就是 true
./gflags_test -debug_bool=true	# 这样也是 true
./gflags_test -debug_bool=1 		# 这样也是 true
./gflags_test -debug_bool=false 	# 0是false
```

## 简单使用实例

```
#include <iostream>
#include <gflags/gflags.h>

DEFINE_string(name, "zhang san", "your name");
DEFINE_int32(age, 18, "your age");

int main(int argc, char** argv) {
	gflags::ParseCommandLineFlags(&argc, &argv, true);
	std::cout << "your name is : " << FLAGS_name
        << ", your age is: " << FLAGS_name << std::endl;
    return 0;
}
```

## 进阶使用

### 1.跨文件调用

如果你想要访问在另一个文件定义的 gflags 变量呢？使用 DECLARE_，它的作用就相当于用 extern 声明变量。为了方便的管理变量，我们推荐在 .cc 或者 .cpp文件中DEFINE 变量，然后只在对应.h 中或者单元测试中 DECLARE 变量。

例如，在foo.cc 定义了一个 gflags 变量 DEFINE_string(name, 'bob', '')，假如你需要在其他文件中使用该变量，那么在 foo.h 中声明 DECLARE_string(name)，然后在使用该变量的文件中 include "foo.h" 就可以。当然，这只是为了更好地管理文件关联，如果你不想遵循也是可以的。

## 2.参数检查

使用方法如下：

#### 定义校验函数：

```
static bool ValidatePort(const char* flagname, int32 value) {
	if (value > 0 && value < 200)   
     	return true;
   	printf("Invalid value for --%s: %d\n", flagname, (int)value);
   	return false;
}
```

### 使用全局 static 变量来注册函数，static 变量会在 main 函数开始时就调用, 在DEFINE下定义

```
DEFINE_int32(age, 18, "your age");
static const bool port_dummy = gflags::RegisterFlagValidator(&FLAGS_port, &ValidatePort);
```

### 在输入不合法是就会报错

### 3.flagfile 使用配置文件

在一个配置文件中写上，在此将其命名为 user.flags:

```
--name="zhangsan"
--age=18
```

使用

```
./gflags_test --flagfile  user.flags 
```

