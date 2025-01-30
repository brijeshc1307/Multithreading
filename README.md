# C++ Multithreading Concepts

---

### Threads++
In every application there is a default thread, which is main(); inside main() we create other threads.
```"A thread is also known as a lightweight process. The idea is to achieve parallelism by dividing a process into multiple threads."```

**Exp.**
1. The browser has multiple tabs that can be different threads.
2. MS Word must be using multiple threads, one thread to format the text and another thread to process inputs (spell checker). 
3. Visual Studio Code editor would be using threading for auto-completing the code (IntelliSense).

---

### Multithreading in C++
Multithreading allows concurrent execution of multiple tasks to improve performance and responsiveness.

**Ways to Create Threads:**
1. Function Pointers
2. Lambda Expressions
3. Functors (Function Objects)
4. Non-static Member Functions
5. Static Member Functions

**Example:**
```cpp
#include <iostream>
#include <thread>

void printMessage() {
    std::cout << "Hello from thread!" << std::endl;
}

int main() {
    std::thread t(printMessage);
    t.join();
    return 0;
}
```

---

### 2. Thread Management

- **`join()`**: Waits for the thread to finish execution.
- **`detach()`**: Allows the thread to run independently.
- **`joinable()`**: Checks if a thread is still joinable.

**Example:**
```cpp
std::thread t(printMessage);
if (t.joinable()) {
    t.join();
}
```

---

### 3. Mutex

A **mutex (mutual exclusion)** is used to prevent race conditions by allowing only one thread to access a critical section at a time.

**Types of Mutexes:**
- `std::mutex`
- `std::timed_mutex`
- `std::recursive_mutex`

**Example:**
```cpp
std::mutex mtx;
void sharedFunction() {
    std::lock_guard<std::mutex> lock(mtx);
    // Critical section
}
```

---

### 4. Condition Variable

A **condition variable** is used to synchronize threads based on certain conditions.

**Example:**
```cpp
std::condition_variable cv;
std::mutex mtx;
bool ready = false;

void waitFunction() {
    std::unique_lock<std::mutex> lock(mtx);
    cv.wait(lock, [] { return ready; });
    std::cout << "Condition met!" << std::endl;
}

void signalFunction() {
    std::lock_guard<std::mutex> lock(mtx);
    ready = true;
    cv.notify_one();
}
```

---

### 5. Deadlock Prevention

Deadlock occurs when two or more threads are waiting for each other to release resources.

**Solutions:**
1. Lock ordering
2. Try-lock mechanisms
3. Using `std::lock()`

**Example:**
```cpp
std::lock(mtx1, mtx2);
std::lock_guard<std::mutex> lock1(mtx1, std::adopt_lock);
std::lock_guard<std::mutex> lock2(mtx2, std::adopt_lock);
```

---

### 6. Thread Synchronization

Ensures that threads work together in a coordinated manner to avoid race conditions.

**Common Synchronization Mechanisms:**
- Mutexes
- Condition Variables
- Barriers

---

### 7. `std::async` and Task-Based Parallelism

`std::async` allows execution of tasks asynchronously and returns a `std::future` to retrieve the result later.

**Example:**
```cpp
#include <future>

int computeValue(int x) {
    return x * 2;
}

int main() {
    std::future<int> result = std::async(std::launch::async, computeValue, 10);
    std::cout << "Result: " << result.get() << std::endl;
    return 0;
}
```

---

### 8. `std::promise` and `std::future`

Used for passing data between threads in a producer-consumer style.

**Example:**
```cpp
#include <future>

void setValue(std::promise<int>& prom) {
    prom.set_value(42);
}

int main() {
    std::promise<int> prom;
    std::future<int> fut = prom.get_future();
    std::thread t(setValue, std::ref(prom));
    std::cout << "Value: " << fut.get() << std::endl;
    t.join();
    return 0;
}
```

---

## **License**
This project is licensed under the MIT License.

---

Happy Coding!


