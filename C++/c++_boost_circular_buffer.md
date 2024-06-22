# c++ boost circular_buffer

## 一、概述

Boost.Circular_buffer维护了一块连续内存块作为缓存区，当缓存区内的数据存满时，继续存入数据就覆盖掉旧的数据。
它是一个与STL兼容的容器，类似于 std::list或std::deque,并且支持随机存取。circular_buffer 被特别设计为提供固定容量的存储大小。当其容量被用完时，新插入的元素会覆盖缓冲区头部或尾部(取决于使用何种插入操作)的元素。逻辑存储结构如图

![这里写图片描述](media/20170322112825204)

circular_buffer为了效率考虑，使用了连续内存块保存元素

1. 使用固定内存，没有隐式或者非期望的内存分配
2. 快速在circular_buffer头或者尾部插入，删除元素，并且是常量时间复杂度
3. 常量时间访问元素
4. 适合实时和对性能要求苛刻的应用

## 二、代码

头文件：

```
#include <boost/circular_buffer.hpp> 
```

示例：

```c++
#include <boost/circular_buffer.hpp>
#include <numeric>
#include <assert.h>

int main(int /*argc*/, char* /*argv*/[])
{
    // 创建一个容量为3的循环缓冲区
    boost::circular_buffer<int> cb(3);

    // 插入一些元素到循环缓冲区
    cb.push_back(1);
    cb.push_back(2);

    // 断言
    assert(cb[0] == 1);
    assert(cb[1] == 2);
    assert(!cb.full());
    assert(cb.size() == 2);
    assert(cb.capacity() == 3);

    // 再插入其它元素
    cb.push_back(3);
    cb.push_back(4);

    // 求和
    int sum = std::accumulate(cb.begin(), cb.end(), 0);

    // 断言
    assert(cb[0] == 2);
    assert(cb[1] == 3);
    assert(cb[2] == 4);
    assert(*cb.begin() == 2);
    assert(cb.front() == 2);
    assert(cb.back() == 4);
    assert(sum == 9);
    assert(cb.full());
    assert(cb.size() == 3);
    assert(cb.capacity() == 3);

    return 0;
}c++
```

## 三、分析过程

从使用上看，它和普通的STL容器没什么两样。circular_buffer在执行本例代码过程状态如下：

```c
// 创建一个容量为3的循环缓冲区 
 boost::circular_buffer cb(3);
```

这时里面是没有数据的:
cb.size() == 0;
cb.capacity()==3;
cb.empty()==true;
cb.full()==false;
![这里写图片描述](media/20170322113142191)

// 插入一些元素到循环缓冲区
cb.push_back(1);
cb.size() == 1;
cb.capacity()==3;
cb.empty()==false;
cb.full()==false;

![这里写图片描述](media/20170322113220859)

cb.push_back(2);
cb.size() == 2;
cb.capacity()==3;
cb.empty()==false;
cb.full()==false;

![这里写图片描述](media/20170322113301504)

// 再插入其它元素
cb.push_back(3);
cb.size() == 3;
cb.capacity()==3;
cb.empty()==false;
cb.full()==true;

![这里写图片描述](media/20170322113321860)

cb.push_back(4);
cb.size() == 3;
cb.capacity()==3;
cb.empty()==false;
cb.full()==true;
因为已到容量上限，所以数据4覆盖了旧数据1，并且begin()和end()都向前移一格。所以这时：
cb[0]==2;
cb[1]==3;
cb[2]==4;

![这里写图片描述](media/20170322113338489)

我们也可以把它设想为一个定长的队列，当在队列满的情况下再向队尾放入数据时，就把队首“挤”出队列，反之亦然。

## 四、示例

```c++
#include<iostream>
using namespace std;
#include<boost/circular_buffer.hpp>
using namespace  boost;

int main()
{
    // 定义并初始化一个循环缓冲区
    circular_buffer<int>  cb(3);//容量为3
    
	cout << cb.capacity() << endl; // 3
    cout << cb.size() << endl;  // 0
    cb.push_back(1);//从尾部插入
    cb.push_back(2);//
    cb.push_back(3);//容量已满
    cout << cb.capacity() << endl; // 3
    cout << cb.size() << endl; // 3

    //cb.push_front(1);//从头插入
    //cb.push_front(2);//
    //cb.push_front(3);//容量已满

    for (int i = 0; i < cb.size(); ++i) 	cout << cb[i] << "   ";
    cout << endl;


    //容量已满，尾部插入，踢出头部元素
    cb.push_back(4);
    for (int i = 0; i < cb.size(); ++i) 	cout << cb[i] << "   ";
    cout << endl;

    //容量已满，头部插入，踢出尾部元素
    cb.push_front(5);
    for (int i = 0; i < cb.size(); ++i) 	cout << cb[i] << "   ";
    cout << endl;

    cb.pop_back();//删除尾部的元素
    for (int i = 0; i < cb.size(); ++i) 	cout << cb[i] << "   ";
    cout << endl;

    cb.pop_front();//删除头部的元素
    for (int i = 0; i < cb.size(); ++i) 	cout << cb[i] << "   ";
    cout << endl;

    return 0;
}
```

## 五、可能适用的场景

1. 可存储最新接收到的samples，当更新的samples到来，覆写最老的元素
2. 可用作底层容器实现固定大小buffer
3. 可作为一种cache，保存一定数量的最新插入的元素
4. 高效的固定大小先进先出队列
5. 高效的后进先去队列，当队列满时，移除最老的元素（也就是第一个插入的元素）