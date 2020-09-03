### std::lock_guard作用域内自动上锁解锁

对于不同线程访问统一资源时，为了避免冲突一般都通过对目标共享变量上锁和解锁，让共享变量互斥

- 第一种方式：一般情况可以在共享变量前后分别上锁解锁，至少需要以下三个操作

```cpp
// 定义锁
std::mutex m_mutex;

// 上锁
m_mutex.lock();

// 上锁和解锁之间为对共享变量的访问操作.....

// 解锁
m_mutex.unlock();
```

- 第二种方式：使用std::lock_guard，在std::lock_guard对象的作用域内进行互斥量的操作，例如：

```cpp

#include <iostream>
#include <mutex>
#include <thread>
#include <windows.h>

//全局变量，两个线程都会访问
int g_num = 0;
//定义锁
std::mutex m_mutex;

void bar()
{
    //函数bar()范围内，自动为互斥量上锁和解锁
    std::lock_guard<std::mutex> LockGuard(m_mutex);

    std::cout << "This thread id is : " << std::this_thread::get_id() << "  --  g_num : " << g_num << std::endl;
    g_num++;
}

void foo()
{
    while (1) {
        bar();
        Sleep(1000);
    }
}

int main()
{
    std::thread first(foo);     // thread first
    std::thread second(foo);    // thread second

    first.join();               // pauses until first finishes
    second.join();              // pauses until second finishes

    return 0;
}
```

std::lock_guard需要在作用域范围开头定义，也可以通过块操作限制其作用域范围，例如：

```cpp
void func()
{
	....

    {	// 范围起始
    std::lock_guard<std::mutex> LockGuard(m_mutex);

    }	// 范围结束

	....
}
```
