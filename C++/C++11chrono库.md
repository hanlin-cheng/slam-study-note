# C++11:chrono库 (日期和时间库)

> ​	C++11标准已经支持std::chrono了，但是为了兼容老编译系统现在很多C++库和程序都使用boost::chrono作为时间类库(还有的原因就std::chrono没有收录boost::chrono的所有功能，比如统计CPU使用时间、自定义时间输出格式等)，Boost::Chrono的时间类型分为duration和time_point，也就是时长和时刻两类，很多概念和接口都是围绕这两个维度去定义和实现的。

chrono库主要包含了三种类型:
时间间隔Duration
时钟Clocks
时间点Time point

## **Duration**

duration表示一段时间间隔，用来记录时间长度，可以表示几秒钟、几分钟或者几个小时的时间间隔。

其原型:

```c++
template<class Rep,class Period = std::ration<1>> class duration;
```

第一个模板参数Rep是一个数值类型，表明存储所用的数据类型(int、long、double等)；第二个模板参数是一个默认模板参数std::ratio，它的原型是：

```c++
template<std::intmax_t Num, std::intmax_t Denom = 1> class ratio;
```

第一个模板参数Rep是一个数值类型，表明存储所用的数据类型(int、long、double等)；第二个模板参数是一个默认模板参数std::ratio，它的原型是：

它表示每个时钟周期的秒数，其中第一个模板参数Num代表分子，Denom代表分母，分母默认为1，ratio代表的是一个分子除以分母的分数值，比如ratio<2>代表一个时钟周期是两秒，ratio<60>代表了一分钟，ratio<60 * 60>代表一个小时，ratio<60 * 60 * 24>代表一天。而ratio<1, 1000>代表的则是1/1000秒即一毫秒，ratio<1, 1000000>代表一微秒，ratio<1, 1000000000>代表一纳秒。

标准库为了方便使用，就定义了一些常用的时间间隔，如时、分、秒、毫秒、微秒和纳秒，在chrono命名空间下，它们的定义如下：

```c++
typedef duration <Rep, ratio<3600,1>> hours;

typedef duration <Rep, ratio<60,1>> minutes;

typedef duration <Rep, ratio<1,1>> seconds;

typedef duration <Rep, ratio<1,1000>> milliseconds;

typedef duration <Rep, ratio<1,1000000>> microseconds;

typedef duration <Rep, ratio<1,1000000000>> nanoseconds;
```

```c++
#include <chrono>
#include <iostream>
using namespace std;
int main()
{
    chrono::hours h(1); // 一小时
    chrono::milliseconds ms{ 3 }; // 3 毫秒
    chrono::duration<int, ratio<1000>> ks(3); // 3000 秒

    // chrono::duration<int, ratio<1000>> d3(3.5); // error
    chrono::duration<double> dd(6.6); // 6.6 秒

    // 使用小数表示时钟周期的次数
    chrono::duration<double, std::ratio<1, 30>> hz(3.5);
    return 0;
}

/*********************************************************************
h(1) 时钟周期为 1 小时，共有 1 个时钟周期，所以 h 表示的时间间隔为 1 小时
ms(3) 时钟周期为 1 毫秒，共有 3 个时钟周期，所以 ms 表示的时间间隔为 3 毫秒
ks(3) 时钟周期为 1000 秒，一共有三个时钟周期，所以 ks 表示的时间间隔为 3000 秒
d3(3.5) 时钟周期为 1000 秒，时钟周期数量只能用整形来表示，但是此处指定的是浮点数，因此语法错误
dd(6.6) 时钟周期为默认的 1 秒，共有 6.6 个时钟周期，所以 dd 表示的时间间隔为 6.6 秒
hz(3.5) 时钟周期为 1/30 秒，共有 3.5 个时钟周期，所以 hz 表示的时间间隔为 1/30*3.5 秒
chrono 库中根据 duration 类封装了不同长度的时钟周期（也可以自定义），基于这个时钟周期再进行周期次数的设置就可以得到总的时间间隔了（时钟周期 * 周期次数 = 总的时间间隔）。
*************************************************************************/
```

chrono还提供了获取时间间隔的时钟周期个数的方法count()，它的基本用法：

```c++
#include <chrono>
#include <iostream>
int main()
{
    std::chrono::milliseconds ms{ 3 }; // 3 毫秒
    std::chrono::microseconds us = 2 * ms; // 6000 微秒
    // 时间间隔周期为 1/30 秒
    std::chrono::duration<double, std::ratio<1, 30>> hz(3.5);

    std::cout << "3 ms duration has " << ms.count() << " ticks\n"
        << "6000 us duration has " << us.count() << " ticks\n"
        << "3.5 hz duration has " << hz.count() << " ticks\n";
    return 0;
}
```

```c++
3 ms duration has 3 ticks
6000 us duration has 6000 ticks
3.5 hz duration has 3.5 ticks

ms 时间单位为毫秒，初始化操作 ms{3} 表示时间间隔为 3 毫秒，一共有 3 个时间周期，每个周期为 1 毫秒
us 时间单位为微秒，初始化操作 2*ms 表示时间间隔为 6000 微秒，一共有 6000 个时间周期，每个周期为 1 微秒
hz 时间单位为秒，初始化操作 hz(3.5) 表示时间间隔为 1/30*3.5 秒，一共有 3.5 个时间周期，每个周期为 1/30 秒
```

通过定义这些常用的时间间隔类型，我们能方便的使用它们，比如线程的休眠:

```c++
std::this_thread::sleep_for(std::chrono::seconds(3)); //休眠三秒

std::this_thread::sleep_for(std::chrono:: milliseconds (100)); //休眠100毫秒
```

由于在 duration 类内部做了操作符重载，因此时间间隔之间可以直接进行算术运算，比如我们要计算两个时间间隔的差值，就可以在代码中做如下处理：

```c++
#include <iostream>
#include <chrono>
using namespace boost;

int main()
{
    chrono::minutes t1(10);
    chrono::seconds t2(60);
    chrono::seconds t3 = t1 - t2;
    cout << t3.count() << " second" << endl;
    return 0;
}
```

```c++
540 second
```

在上面的测试程序中，t1 代表 10 分钟，t2 代表 60 秒，t3 是 t1 减去 t2，也就是 60*10-60=540，这个 540 表示的时钟周期，每个时钟周期是 1 秒，因此两个时间间隔之间的差值为 540 秒。

注意事项：duration 的加减运算有一定的规则，当两个 duration 时钟周期不相同的时候，会先统一成一种时钟，然后再进行算术运算.

因为类型表示的维度不一，粗粒度的时长肯定能用细粒度的类型表示，反之则可能丢失精度，所以需要使用`chrono::duration_cast()`函数做显式的转换。将当前的时钟周期转换为其它的时钟周期，比如我可以把秒的时钟周期转换为分钟的时钟周期，然后通过count来获取转换后的分钟时间间隔：

```c++
cout << chrono::duration_cast<chrono::minutes>( t3 ).count() <<” minutes”<< endl;

将会输出:
9 minutes
```

## **Time point**

time_point表示一个时间点，用来获取1970.1.1以来的秒数和当前的时间, 可以做一些时间的比较和算术运算，可以和ctime库结合起来显示时间。time_point必须要clock来计时，

time_point有一个函数`time_from_eproch()`用来获得1970年1月1日到time_point时间经过的duration。

有一个`chrono::time_point_cast`转换函数，可以显式从高粒度向低粒度对time_point进行转换。

下面的例子计算当前时间距离1970年1月1日有多少天：

```c++
#include <iostream>
#include <ratio>
#include <chrono>

int main ()
{
  using namespace std::chrono;
  typedef duration<int,std::ratio<60*60*24>> days_type;
  time_point<system_clock,days_type> today = time_point_cast<days_type>(system_clock::now());
  std::cout << today.time_since_epoch().count() << " days since epoch" << std::endl;

  return 0;
}
```

time_point还支持一些算术元算，比如两个time_point的差值时钟周期数，还可以和duration相加减。下面的例子输出前一天和后一天的日期：

```c++
#include <iostream>
#include <iomanip>
#include <ctime>
#include <chrono>

int main()
{
    using namespace std::chrono;
    system_clock::time_point now = system_clock::now();
    std::time_t last = system_clock::to_time_t(now - std::chrono::hours(24));

　　std::time_t next= system_clock::to_time_t(now - std::chrono::hours(24));

    std::cout << "One day ago, the time was "<< std::put_time(std::localtime(&last), "%F %T") << '\n';

　　std::cout << "Next day, the time was "<< std::put_time(std::localtime(&next), "%F %T") << '\n';

}

输出：
One day ago, the time was 2014-3-2622:38:27
Next day, the time was 2014-3-2822:38:27
```

## **Clocks**

clock是Chrono中的重要概念，而且这些clock都包含一个`now()`的成员函数，用于返回当前的time_point。Boost.Chrono包含的clock类型有：

　　(1) `chrono::system_clock` 代表系统时间，比如电脑上显示的当前时间，其特点是这个时间可以被用户手动设置更新，所以这个时钟是可以和外部时钟源同步的。

　　(2) `chrono::steady_clock` 其特点是时间是单调增长的，后一个时刻访问得到的时间点肯定比之前时刻得到的时间点要晚，即使我们手动将系统时间向前调整了也不会改变这个时钟稳步向前推行累计，其也被称为monotonic time，该时钟是均匀增长且不能被调整，其特性对于很多不允许时间错乱的系统是十分重要的。`chrono::steady_clock`通常是基于系统启动时间来计时的，而且常常用来进行耗时、等待等工作使用。**steady_clock可以获取稳定可靠的时间间隔，后一次调用now()的值和前一次的差值是不因为修改了系统时间而改变，它保证了稳定的时间间隔。它的用法和system用法一样。**

　　(3) `chrono::high_resolution_clock `依赖于系统实现，通常是上面两种时钟的某个宏定义，取决于哪个时钟源更为的精确，所以其输出也决定于取决于上面哪个clock来实现的。

　　(4) `chrono::process_real_cpu_clock `表示自进程启动以来使用的CPU时间，而这个数据也可以通过使用std::clock()来获得。`chrono::process_user_cpu_clock`、`boost::chrono::process_system_cpu_clock`表示自进程启动以来，在用户态、内核态所花费的时间，而所有的这些事件可以通过`chrono::process_cpu_clock`来获得，他返回上面所有时间组成的一个tuple结构。

　　(5) `chrono::thread_clock` 返回基于线程统计的花费时间，而且不区分用户态、内核态的时间。

在这些时钟类的内部有 time_point、duration、Rep、Period 等信息，基于这些信息来获取当前时间。

可以通过now()来获取当前时间点：

```c++
#include <iostream>
#include <chrono>

int main()
{
std::chrono::steady_clock::time_point t1 = std::chrono::system_clock::now();

std::cout << "Hello World\n";
std::chrono::steady_clock::time_point t2 = std::chrono:: system_clock::now();

std::cout << (t2-t1).count()<<” tick count”<<endl;
}

输出：
Hello World
20801tick count
```

通过时钟获取两个时间点之相差多少个时钟周期，我们可以通过duration_cast将其转换为其它时钟周期的duration：

```c++
cout << std::chrono::duration_cast<std::chrono::microseconds>( t2-t1 ).count() <<” microseconds”<< endl;

输出：
20 microseconds
```

还可以实现 `time_t` 和 `time_point` 之间的相互转换。

```c++
//system_clock的to_time_t方法可以将一个time_point转换为ctime：
std::time_t now_c = std::chrono::system_clock::to_time_t(time_point);
//而from_time_t方法则是相反的，它将ctime转换为time_point。
```

```c++
#include <chrono>
#include <iostream>
using namespace std;
using namespace std::chrono;
int main()
{
    // 新纪元1970.1.1时间
    system_clock::time_point epoch;

    duration<int, ratio<60 * 60 * 24>> day(1);
    // 新纪元1970.1.1时间 + 1天
    system_clock::time_point ppt(day);

    using dday = duration<int, ratio<60 * 60 * 24>>;
    // 新纪元1970.1.1时间 + 10天
    time_point<system_clock, dday> t(dday(10));

    // 系统当前时间
    system_clock::time_point today = system_clock::now();

    // 转换为time_t时间类型
    time_t tm = system_clock::to_time_t(today);
    cout << "今天的日期是: " << ctime(&tm);

    time_t tm1 = system_clock::to_time_t(today + day);
    cout << "明天的日期是: " << ctime(&tm1);

    time_t tm2 = system_clock::to_time_t(epoch);
    cout << "新纪元时间: " << ctime(&tm2);

    time_t tm3 = system_clock::to_time_t(ppt);
    cout << "新纪元时间+1天: " << ctime(&tm3);

    time_t tm4 = system_clock::to_time_t(t);
    cout << "新纪元时间+10天: " << ctime(&tm4);
    return 0;
}
```

```c++
今天的日期是: Thu Apr 8 11:09:49 2021
明天的日期是: Fri Apr 9 11:09:49 2021
新纪元时间: Thu Jan 1 08:00:00 1970
新纪元时间+1天: Fri Jan 2 08:00:00 1970
新纪元时间+10天: Sun Jan 11 08:00:00 1970
```

### 参考链接

https://blog.csdn.net/weixin_42907473/article/details/90278426

https://www.cnblogs.com/Galesaur-wcy/p/15380832.html

https://www.jb51.net/article/122979.htm