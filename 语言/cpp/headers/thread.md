#### 创建线程

```CPP
void func() { ... }
void func(int x) { ... }
void func(string &str) { ... }

int main() {
	thread t0(func); // 无参数
	thread t1(func, "test"); // 有参数
	thread t2(func, ref(str)); // 有引用参数
}
```

#### join & detach

```CPP
void func() { ... }
int main() {
	thread t0(func);
	thread t1(func);
	// 启动了 t0，主程序不等待它结束
	// 阻塞主线程直到 t0 结束，
    t0.join();
    // 强行 join 可能抛出 system_error
    if (t1.joinable())
	    t1.join();
	
	thread t3(func);
	// 分离线程，t2 会在后台运行，主线程不等待
	t3.detach();
}

// 使用智能指针以解决内存问题
class A {
public:
    void func() { cout << "test"; }
};
int main() {
    shared_ptr<A> a = make_shared<A>();
    thread t(&A::func, a);
		if(t.joinable()) t.join();
}
```

#### mutex

```C++
// 问题：资源竞争
int a = 0;
void func() {
    for (int i = 0; i < 1000000; ++i)
        ++a;
}
int main() {
    thread t1(func);
    thread t2(func);
    t1.join();
    t2.join();
    cout << a;
    // 每次运行 a 的结果不一样
}

// 解决：加互斥锁
int a = 0;
mutex mtx;
void func() {
    for (int i = 0; i < 100000; ++i) {
        mtx.lock(); // 加锁后，其他线程被阻塞
        a += 1;
        mtx.unlock(); // 解锁后，其他线程恢复
    }
}
int main() {
    thread t1(func);
    thread t2(func);
    t1.join();
    t2.join();
    cout << a;
    // 结果正确
}
```

#### 死锁问题

```C++
mutex m1, m2;
// 修改前：发生死锁
void func_1() {
    m1.lock();
    m2.lock();
    m1.unlock();
    m2.unlock();
}
void func_2() {
    m2.lock();
    m1.lock();
    m2.unlock();
    m1.unlock();
}

// 修改后
void func_1() {
    m1.lock();
    m2.lock();
    m1.unlock();
    m2.unlock();
}
void func_2() {
    m1.lock();
    m2.lock();
    m1.unlock();
    m2.unlock();
}

int main() {
    thread t1(func_1);
    thread t2(func_2);
    t1.join();
    t2.join();
    cout << "over";
}
```

#### lock_guard

```C++
// lock_guard 是 mutex 的封装类
// 当构造函数被调用时，该互斥量会自动加锁
// 当析构函数被调用时，该互斥量会自动解锁
// lock_guard 不能复制或移动（源码中 delete 掉了）
int shared_data = 0;
mutex mtx;
void func() {
    for (int i = 0; i < 10000; i++) {
        // lock_guard会自动加锁，解锁
        lock_guard<mutex> lg(mtx);
        ++shared_data;
    }
}
int main() {
    thread t1(func);
    thread t2(func);
    t1.join();
    t2.join();
    cout << shared_data;
}
```

#### unique_lock

```C++
// unique_lock 是 mutex 的封装类
// 可以对互斥量进行更加灵活的管理，包括：
// 延迟加锁、条件变量、超时等

// 自动加锁、解锁：
int shared_data = 0;
mutex mtx;
void func() {
    for (int i = 0; i < 10000; i++) {
        // unique_lock会自动加锁，解锁
        unique_lock<mutex> lg(mtx); // 第二参数是defer_lock，则不立即加锁
        ++shared_data;
    }
}
int main() {
    thread t1(func);
    thread t2(func);
    t1.join();
    t2.join();
    cout << shared_data;
}

// try_lock_for：延时加锁
int shared_data = 0;
timed_mutex tmtx;
void func() {
    for (int i = 0; i < 2; i++) {
        // 构造时就加锁
//        unique_lock<timed_mutex> lg(tmtx);
        // 构造时不加锁
        unique_lock<timed_mutex> lg(tmtx, defer_lock);
        lg.try_lock_for(chrono::seconds(5));
        this_thread::sleep_for(chrono::seconds(10));
        ++shared_data;
    }
}
int main() {
    thread t1(func);
    thread t2(func);
    t1.join();
    t2.join();
    cout << shared_data;
}
///// try_lock_until/尝试获得锁，到某个时间还阻塞则放弃，try_lock_for较为常用
```

## call_once/once_flag

```C++
// 可以用来解决懒汉模式下的线程安全问题
// call_once只能在线程函数中使用，在main中使用会报错
once_flag flag;
mutex mtx;
void init(thread::id id) {
    lock_guard<mutex> lg(mtx);
    cout << "thread: " << id  << "has been initialized." << endl;
}
void func() {
    // 保证只有一个线程调用了init
    call_once(flag, init, this_thread::get_id());
    lock_guard<mutex> lg(mtx);
    cout << this_thread::get_id() << "is processing." << endl;
}
int main() {
    thread th1(func);
    thread th2(func);
    thread th3(func);
    th1.join();
    th2.join();
    th3.join();
}
```

## condition_variable

```C++
#include <mutex>
#include <condition_variable>
// wait : 阻塞当前线程，直到被唤醒
// wait_for ：阻塞当前线程，直到被唤醒，或过了指定时长以后
// wait_until ：阻塞当前线程，直到被唤醒，或到达某时间以后
// notify_one ：通知一个等待的线程
// notify_all ：通知所有等待的线程
mutex mtx;
condition_variable cv;
unique_lock<mutex> lk(mtx);
cv.wait(lk, [&] {return <bool>});
cv.notify_all();
```

## promise & future

```C++
#include <future>
// 一个future对象只能get一次，如需多次，使用shared_future
void func(int end, promise<int> pm) {
    int sum = 0;
    for (int i = 1; i <= end; i++) {
        sum += i;
    }
    pm.set_value(sum);
}
int main() {
    promise<int> pm;
    future<int> sum_future = pm.get_future();
    thread th(func, 100, move(pm));
    cout << sum_future.get() << endl;
    th.join();
}
```

## packaged_task

```C++
#incldue <future>
int func(int start, int end) {
    int sum = 0;
    for (int i = start; i <= end; i++) sum += i;
    return sum;
}
int main() {
    packaged_task<int(int, int)> task(func);
    future<int> sum_future = task.get_future();
    this_thread::sleep_for(chrono::seconds(1));
    cout << "main thread is over here" << endl;
    task(1, 100);
    cout << sum_future.get() << endl;
}
```

## async

```C++
int func(int start, int end) {
    int sum = 0;
    for (int i = start; i <= end; i++) {
        sum += i;
    }
    return sum;
}
int main() {
    future<int> f = async(func, 1, 100);
//    future<int> f = async(launch::async, func, 1, 100); // 延迟开启
    cout
            << "main thread processing..." << endl
            << f.get() << endl;
}
```

## atimoic | 原子操作

```C++
#include <thread>
#include <atomic>
#include <vecter>
#include <chrono>
atomic_int total(0);
void func() {
    for (int i = 0; i < 100000; ++i) {
        total++;
        total--;
    }
}
int main() {
    auto start = chrono::system_clock::now();
    vector<thread> pool;
    for (int i = 0; i < 8; ++i)
        pool.emplace_back(func);
    for (int i = 0; i < 8; ++i)
        pool.at(i).join();
    auto end = chrono::system_clock::now();
    auto dur = chrono::duration_cast<chrono::milliseconds>(end - start);
    cout << dur.count() << endl;
}
```