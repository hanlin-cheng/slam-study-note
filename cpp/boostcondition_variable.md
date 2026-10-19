# boost::condition_variable

boost::condition 和 boost::condition_variable 都是 Boost 库中用于实现条件变量的类。条件变量是一种同步原语，通常用于实现等待/通知机制，以便在多线程环境中协调线程之间的操作。

boost::condition 是较早的类，而 boost::condition_variable 是较新的类，并被视为 boost::condition 的超集。下面是它们之间的主要区别：

使用方式：boost::condition 通常与 boost::mutex 或其他互斥量类型一起使用，用于保护共享数据的访问。它提供了 wait(), wait_for(), wait_until() 等方法来等待条件满足。


```
boost::mutex mtx;  
boost::condition cond;  
bool ready = false;  
  
// 生产者线程  
void producer() {  
    // 做一些工作...  
    mtx.lock();  
    ready = true;  
    cond.notify_one(); // 通知等待的线程  
    mtx.unlock();  
}  
  
// 消费者线程  
void consumer() {  
    mtx.lock();  
    while (!ready) { // 等待条件满足  
        cond.wait(mtx);  
    }  
    // 做一些工作...  
    mtx.unlock();  
}
```

如果需要使用超时等待，可以使用 `wait_for` 方法：

```
#include <iostream>
#include <thread>
#include <mutex>
#include <boost/thread.hpp>
#include <boost/chrono.hpp>

boost::mutex mtx;
boost::condition_variable cv;
bool ready = false;

void print_id(int id) {
    boost::unique_lock<boost::mutex> lck(mtx);
    while (!ready) {
        if(cv.wait_for(lck, boost::chrono::seconds(1)) == boost::cv_status::timeout) {
            std::cout << "Thread " << id << " timed out.\n";
            return;
        }
    }
    // 唤醒后，继续执行
    std::cout << "Thread " << id << '\n';
}

void go() {
    boost::unique_lock<boost::mutex> lck(mtx);
    ready = true;
    cv.notify_all();
}

int main() {
    boost::thread threads[10];
    // 创建10个线程
    for (int i = 0; i < 10; ++i) {
        threads[i] = boost::thread(print_id, i);
    }

    std::cout << "10 threads ready to race...\n";
    boost::this_thread::sleep_for(boost::chrono::seconds(2));
    go(); // 唤醒所有线程

    // 等待所有线程完成
    for (auto& th : threads) {
        th.join();
    }

    return 0;
}
```

在这个示例中，`wait_for` 方法在每次等待时都设置一个1秒的超时时间。如果线程在1秒内没有被唤醒，它将输出一个超时信息并返回。这对于需要超时控制的场景非常有用。

相比之下，boost::condition_variable 也与 boost::mutex 或其他互斥量类型一起使用，但提供了更现代的接口和更多的功能。它提供了与 std::condition_variable 类似的接口，包括 wait(), wait_for(), wait_until() 等方法。

接口：boost::condition_variable 具有更现代的接口，并提供了更多的功能，例如 notify_all() 方法，用于通知所有等待的线程。此外，它还支持在通知之前设置一个谓词（即一个返回布尔值的函数），以便只有在谓词为真时才通知等待的线程。这提供了更大的灵活性。
性能：boost::condition_variable 在某些实现中可能比 boost::condition 具有更好的性能。这主要是因为它使用了更现代的同步原语和更优化的内部逻辑。
可移植性：由于 boost::condition_variable 是较新的类，它与更多的编译器和平台兼容性更好。然而，这可能会因具体的 Boost 版本和平台而异。
总的来说，如果您正在使用较新的 Boost 版本并且想要使用更现代的条件变量接口，那么 boost::condition_variable 是一个更好的选择。如果您正在使用较旧的 Boost 版本或者需要与特定的编译器和平台兼容，那么 boost::condition 可能是一个更合适的选择。
