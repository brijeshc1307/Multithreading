# C++ Multithreading Concepts

---

### Threads
In every application there is a default thread, which is main(); inside main() we create other threads.
```"A thread is also known as a lightweight process. The idea is to achieve parallelism by dividing a process into multiple threads."```

**Exp.**
1. The browser has multiple tabs that can be different threads.
2. MS Word must be using multiple threads, one thread to format the text and another thread to process inputs (spell checker). 
3. Visual Studio Code editor would be using threading for auto-completing the code (IntelliSense).

---

### A. Multithreading in C++
Multithreading allows concurrent execution of multiple tasks to improve performance and responsiveness.
```Note: If we create multiple threads to the same time it doesn't gaurantee which one will start first.```

**Ways to Create Threads:**
1. Function Pointers
2. Lambda Expressions
3. Functors (Function Objects)
4. Non-static Member Functions
5. Static Member Functions
---

### 1. Function Pointers
This is very basic form of thread creation
```cpp
#include <iostream>
#include <thread>
using namespace std;

void fun(int x){
    while(x-->0){
    cout<<x<<endl;
    }
}
int main(){
thread t1(fun, 10);
t1.join();
return 0;
}
```
```txt
9
8
7
6
5
4
3
2
1
0
```

```cpp
#include <iostream>
#include <thread>

void printMessage() {
    cout << "Hello from thread!" << endl;
}

int main() {
    thread t(printMessage);
    t.join();
    return 0;
}
```
```
Hello from thread!
```
---

### 2. Lambda Expressions
Lambda expression allows us to define anonymous function objects (functors) which can either be used inline or passed as an argument.
```cpp
#include <iostream>
#include <thread>
using namespace std;

int main() {
    // we can directly inject lambda at thread creation time.
    
    auto fun=[](int x){
        while(x-->0){
            cout<<x<<endl;
        }
    };
    thread t(fun, 10);
    t.join();
    return 0;
}
```
```txt
9
8
7
6
5
4
3
2
1
0
```
---

### 3. Functors (Function Objects)
A C++ functor (function object) is a class or struct object that can be called like a function.
```cpp
#include <iostream>
#include <thread>
using namespace std;

class Base{
    public:
    void operator () (int x){
       while(x-->0){
        cout<<x<<endl;
        } 
    }
};

int main(){
thread t1(Base(), 10);
t1.join();
return 0;
}
```
```txt
9
8
7
6
5
4
3
2
1
0
```
---

### 4. Non-static Member Functions
A non-static member function is a function that is declared in a member specification of a class without a static or friend specifier.
```cpp
#include <iostream>
#include <thread>
using namespace std;

class Base {
public:
    void run(int x) {
        while (x-- > 0) {
            cout << x << endl;
        }
    }
};

int main() {
    Base b;
    thread t1(&Base::run, &b, 10); // Correct syntax
    t1.join();
    return 0;
}
```
```txt
9
8
7
6
5
4
3
2
1
0
```

---

### 5. Static Member Functions
A static member function in C++ is a function that belongs to a class rather than any specific object of the class. It can be called using the class name and does not require an instance of the class.
```cpp
#include <iostream>
#include <thread>
using namespace std;

class Base {
public:
    static void run(int x) { // Made run() static
        while (x-- > 0) {
            cout << x << endl;
        }
    }
};

int main() {
    thread t1(&Base::run, 10); // No need to pass an instance
    t1.join();
    return 0;
}
```
```txt
9
8
7
6
5
4
3
2
1
0
```
---

### B. Thread Management

- **`join()`**: Waits for the thread to finish execution. join() use for join the process with main().
  ```Note: Double t.join() is not allow, It showing en error "terminate called after throwing an instance of 'std::system_error"```
```cpp
#include <iostream>
#include <thread>
#include <chrono> // Required for std::chrono::seconds
using namespace std;

// Function to be executed by the thread
void run(int count) {
    while (count-- > 0) {
        cout<<"Hello"<<endl;
        this_thread::sleep_for(chrono::seconds(5)); // Pause for 1 second in each iteration
    }
}

int main() {
    thread t1(run, 10);   // Create a thread t1 that runs the 'run' function with an argument of 10
    cout << "main() " << endl;  // Print message from the main thread
    t1.join(); // Wait for t1 to complete execution
    cout << "main() after " << endl;  // Print message after t1 has finished
    return 0;
}
```
```txt
main() 
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
main() after 
```

- **`detach()`**: Allows the thread to run independently.
  ```Note: Double t.detach() is not allow, It showing en error "terminate called after throwing an instance of 'std::system_error"```
```cpp
#include <iostream>
#include <thread>
#include <chrono> // Required for std::chrono::seconds
using namespace std;

// Function to be executed by the thread
void run(int count) {
    while (count--> 0) {
        cout<<"Hello"<<endl;
        this_thread::sleep_for(chrono::seconds(5)); // Pause for 1 second in each iteration
        //cout<<"Thread finished"<<endl;
        
    }
}

int main() {
    thread t1(run, 10);   // Create a thread t1 that runs the 'run' function with an argument of 10
    cout << "main() " << endl;  // Print message from the main thread
    t1.detach(); // Wait for t1 to complete execution
    //t1.detach();
    cout << "main() after " << endl;  // Print message after t1 has finished
    return 0;
}
```
```txt
main() 
Hello
main() after

If we print cout<<"Thread finished"<<endl; then output
main() 
main() after

Beacause we detached our thread t1. we are not saying that wait for complete so we got above result.
```

- **`joinable()`**: Checks if a thread is still joinable.

**Example:**
```cpp
thread t(printMessage);
if (t.joinable()) {
    t.join();
}
```

---
```cpp
#include <iostream>
#include <thread>
#include <chrono> // Required for std::chrono::seconds
using namespace std;

// Function to be executed by the thread
void run(int count) {
    while (count--> 0) {
        cout<<"Hello"<<endl;
         // Pause for 1 second in each iteration
        //cout<<"Thread finished"<<endl;
        
    }
}

int main() {
    thread t1(run, 10);   // Create a thread t1 that runs the 'run' function with an argument of 10
    cout << "main() " << endl;  // Print message from the main thread
    t1.detach(); // Wait for t1 to complete execution
    //t1.detach();
    cout << "main() after " << endl;  // Print message after t1 has finished
    this_thread::sleep_for(chrono::seconds(5));
    return 0;
}
```
```txt
main() 
main() after 
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
```
---

### **Notes on `std::this_thread` Functions in C++**  

#### **1. `std::this_thread::sleep_for()`**  
- **Purpose:** Pauses the current thread for a specified duration.  
- **Usage:**  
  ```cpp
  std::this_thread::sleep_for(std::chrono::seconds(2)); // Sleep for 2 seconds
  ```  
- **Use Case:** Useful for implementing delays or throttling execution.  

#### **2. `std::this_thread::sleep_until()`**  
- **Purpose:** Pauses the current thread until a specified point in time.  
- **Usage:**  
  ```cpp
  auto wake_up_time = std::chrono::system_clock::now() + std::chrono::minutes(1);
  std::this_thread::sleep_until(wake_up_time); // Sleep until the specified time
  ```  
- **Use Case:** Ideal for scheduling tasks at a specific future time.  

#### **3. `std::this_thread::yield()`**  
- **Purpose:** Suggests to the OS that the current thread should pause and allow another thread to run.  
- **Usage:**  
  ```cpp
  std::this_thread::yield();
  ```  
- **Use Case:** Helps improve CPU efficiency in multithreading by preventing busy waiting.  

#### **4. `std::this_thread::get_id()`**  
- **Purpose:** Retrieves the unique ID of the currently executing thread.  
- **Usage:**  
  ```cpp
  std::thread::id this_id = std::this_thread::get_id();
  std::cout << "Thread ID: " << this_id << std::endl;
  ```  
- **Use Case:** Useful for debugging and tracking thread execution.  

---

### **Mutexes and RAII Wrappers in C++**  

#### **1. Mutex Classes in C++ (`<mutex>` and `<shared_mutex>`)**  
The C++ Standard Library provides multiple types of mutexes for thread synchronization:  

- **`std::mutex`** – Basic mutex for mutual exclusion. Only one thread can lock it at a time.  
- **`std::recursive_mutex`** – Allows the same thread to lock the mutex multiple times without causing a deadlock. Useful for recursive functions.  
- **`std::shared_mutex`** – Supports both **exclusive** (write) and **shared** (read) locks, allowing multiple threads to read concurrently while ensuring exclusive access for writes.  

#### **2. RAII Wrappers for Mutexes**  
RAII (Resource Acquisition Is Initialization) wrappers help automatically manage the locking and unlocking of mutexes:  

- **`std::unique_lock`** – A flexible RAII wrapper for exclusive locking of `std::mutex`.  
  - Allows **deferred locking, ownership transfer, and timed locking**.  
- **`std::shared_lock`** – A RAII wrapper for **shared (read-only) locking** with `std::shared_mutex`.  

#### **Key Benefits of Using RAII Wrappers**  
- **Automatic unlocking** when the lock object goes out of scope.  
- **Exception safety** – prevents resource leaks even if an exception occurs.  
- **Better flexibility** compared to manually using `lock()` and `unlock()`. 

---

### **1. `std::mutex`** 

A **mutex (mutual exclusion)** is used to prevent Race Conditions by allowing only one thread to access a critical section at a time.

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
### **Race Condition**  

A **race condition** occurs when multiple threads access and modify shared data simultaneously, leading to unpredictable behavior. This happens when proper synchronization mechanisms (like mutexes) are not used. 
```"Race condition is a situation where two or more threads/ process happend to change a common data at the same time." ```

```cpp
#include <iostream>
#include <thread>

using namespace std;

int counter = 0; // Shared resource

void increment() {
    for (int i = 0; i < 100000; i++) {
        counter++; // Race condition occurs here
    }
}

int main() {
    thread t1(increment);
    thread t2(increment);

    t1.join();
    t2.join();

    cout << "Final Counter Value: " << counter << endl; // Unexpected output due to race condition
    return 0;
}
```
```txt
Final Counter Value: 149951
```
### **Expected vs. Actual Output**
- **Expected:** `counter` should be `200000` (100000 increments per thread).  
- **Actual:** The result varies each time due to **race conditions**, often producing a number **less than 200000** because both threads modify `counter` without synchronization.

---

### **Fixing Race Condition using `std::mutex`**
To prevent race conditions, use a **mutex (`std::mutex`)** to ensure that only one thread modifies `counter` at a time.

```cpp
#include <iostream>
#include <thread>
#include <mutex>

using namespace std;

int counter = 0; 
mutex mtx; // Mutex to protect shared resource

void increment() {
    for (int i = 0; i < 100000; i++) {
        mtx.lock();   // Lock before modifying shared data
        counter++;    // Critical section
        mtx.unlock(); // Unlock after modification
    }
}

int main() {
    thread t1(increment);
    thread t2(increment);

    t1.join();
    t2.join();

    cout << "Final Counter Value: " << counter << endl; // Always 200000
    return 0;
}
```
```txt
Final Counter Value: 200000
```
---

### **Key Takeaways**
   - **Race Condition Definition:**  Occurs when multiple threads access and modify shared data concurrently, leading to unpredictable results.     
   - **Why it Happens:**  CPU switches between threads at unpredictable times, causing lost updates to shared variables.  
   - **Solution:**  Use **`std::mutex`** to lock critical sections and prevent concurrent modification., Alternative: **`std::atomic<int>`** can also be used for simple integer operations.
   - **Notes:** _"If there is a race condition then we have protect it, and protected section is called critical section/ region. We use lock, unlock to avoid race condition."_
      
---
### **2. `std::recursive_mutex`**  
A `std::recursive_mutex` allows the **same thread** to lock the mutex **multiple times** without causing a deadlock. This is useful in **recursive functions**, where a function calls itself and needs to acquire the same lock multiple times.  

#### **Example: `std::recursive_mutex`**
```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::recursive_mutex rec_mutex;

void recursive_function(int count) {
    if (count <= 0) return;

    rec_mutex.lock(); // Lock the mutex
    std::cout << "Lock acquired for count: " << count << std::endl;
    
    recursive_function(count - 1); // Recursive call

    rec_mutex.unlock(); // Unlock the mutex
    std::cout << "Lock released for count: " << count << std::endl;
}

int main() {
    std::thread t1(recursive_function, 3);
    t1.join();
    return 0;
}
```

#### **Output:**
```
Lock acquired for count: 3
Lock acquired for count: 2
Lock acquired for count: 1
Lock released for count: 1
Lock released for count: 2
Lock released for count: 3
```

#### **Why Use `std::recursive_mutex`?**
- Without `recursive_mutex`, calling `lock()` multiple times within the same thread **causes a deadlock**.
- `std::recursive_mutex` allows a thread to **re-enter a locked mutex** multiple times safely.

---

### **3. `std::shared_mutex`**
A `std::shared_mutex` allows **multiple threads to read** the shared resource **simultaneously**, but **only one thread** can **write** at a time.  
- Multiple readers (shared lock) are allowed.
- Writers (exclusive lock) block other threads.

#### **Example: `std::shared_mutex`**
```cpp
#include <iostream>
#include <thread>
#include <shared_mutex>

std::shared_mutex shared_mtx;
int shared_resource = 0;

void reader(int id) {
    std::shared_lock<std::shared_mutex> lock(shared_mtx); // Shared (read) lock
    std::cout << "Reader " << id << " read value: " << shared_resource << std::endl;
}

void writer(int value) {
    std::unique_lock<std::shared_mutex> lock(shared_mtx); // Exclusive (write) lock
    shared_resource = value;
    std::cout << "Writer updated value to: " << shared_resource << std::endl;
}

int main() {
    std::thread r1(reader, 1);
    std::thread r2(reader, 2);
    std::thread w(writer, 42);

    r1.join();
    r2.join();
    w.join();

    return 0;
}
```

#### **Possible Output:**
```
Reader 1 read value: 0
Reader 2 read value: 0
Writer updated value to: 42
```

#### **Why Use `std::shared_mutex`?**
- Allows **multiple readers** at the same time, improving performance.
- Ensures **only one writer** at a time to maintain data integrity.

---
### **Key Differences Between `std::mutex`, `std::recursive_mutex`, and `std::shared_mutex`**

| Feature                | `std::mutex`                  | `std::recursive_mutex`          | `std::shared_mutex`               |
|------------------------|-----------------------------|--------------------------------|------------------------------------|
| **Purpose**            | Basic mutual exclusion      | Allows the same thread to lock multiple times | Supports multiple readers but one writer |
| **Locking Mechanism**  | Single lock per thread      | Multiple locks by the same thread allowed | Shared (read) & Exclusive (write) locks |
| **Reentrancy**         | **Not allowed** (deadlock if locked again by same thread) | **Allowed** (same thread can lock multiple times) | **Not applicable** |
| **Usage Scenario**     | Simple thread synchronization | Recursive functions needing multiple locks | Reader-writer synchronization |
| **Performance**        | Moderate                    | Slightly slower due to multiple locks | Best for read-heavy workloads |
| **Allows Multiple Readers?** | ❌ No (only one thread can access) | ❌ No (only one thread can access) | ✅ Yes (multiple threads can read simultaneously) |
| **Allows Multiple Writers?** | ❌ No                    | ❌ No                            | ❌ No (only one writer at a time) |
| **Header File**        | `<mutex>`                   | `<mutex>`                      | `<shared_mutex>` |
| **Use Case Example**   | Thread-safe resource access | Recursive function locking | Optimizing read-heavy applications |

---

### **Understanding `std::unique_lock` and `std::shared_lock` in C++**  
C++ provides **RAII (Resource Acquisition Is Initialization)** wrappers to manage mutex locking efficiently.  

---

## **1. `std::unique_lock`**
- A **flexible RAII wrapper** for `std::mutex`.  
- Provides **exclusive** access to a shared resource.  
- More **feature-rich** than `std::lock_guard` (supports deferred locking, try-locking, and timed locking).  

### **Example: Using `std::unique_lock` for Exclusive Locking**
```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::mutex mtx; // Standard mutex
int counter = 0; // Shared resource

void increment() {
    std::unique_lock<std::mutex> lock(mtx); // Exclusive lock
    counter++;  
    std::cout << "Thread " << std::this_thread::get_id() << " incremented counter to " << counter << std::endl;
    // Lock is automatically released when lock goes out of scope
}

int main() {
    std::thread t1(increment);
    std::thread t2(increment);

    t1.join();
    t2.join();

    std::cout << "Final Counter Value: " << counter << std::endl;
    return 0;
}
```
### **Key Features of `std::unique_lock`**
- **Locks automatically** when created.
- **Unlocks automatically** when out of scope.
- Supports:
  - **Deferred locking**: `std::unique_lock<std::mutex> lock(mtx, std::defer_lock);`
  - **Try-locking**: `lock.try_lock()`
  - **Timed locking**: `lock.try_lock_for(std::chrono::milliseconds(100));`

---

## **2. `std::shared_lock`**
- Works with `std::shared_mutex` (from `<shared_mutex>` header).  
- Allows **multiple threads** to read a shared resource **simultaneously**.  
- Prevents multiple threads from writing at the same time.  

### **Example: Using `std::shared_lock` for Shared Read Access**
```cpp
#include <iostream>
#include <thread>
#include <shared_mutex>

std::shared_mutex sharedMtx;
int sharedData = 100;

void reader(int id) {
    std::shared_lock<std::shared_mutex> lock(sharedMtx); // Shared read lock
    std::cout << "Reader " << id << " read value: " << sharedData << std::endl;
}

void writer(int value) {
    std::unique_lock<std::shared_mutex> lock(sharedMtx); // Exclusive write lock
    sharedData = value;
    std::cout << "Writer updated value to: " << sharedData << std::endl;
}

int main() {
    std::thread r1(reader, 1);
    std::thread r2(reader, 2);
    std::thread w(writer, 500);

    r1.join();
    r2.join();
    w.join();

    return 0;
}
```

### **Key Features of `std::shared_lock`**
- **Multiple threads can acquire a shared lock simultaneously.**
- **Writers get exclusive access** while blocking readers.
- **Optimized for read-heavy operations** like caching or logging.

---

## **Key Differences Between `std::unique_lock` and `std::shared_lock`**
| Feature          | `std::unique_lock`        | `std::shared_lock`          |
|-----------------|-------------------------|---------------------------|
| **Lock Type**    | Exclusive (write) lock  | Shared (read) lock       |
| **Allows Multiple Threads?** | ❌ No (only one writer) | ✅ Yes (multiple readers) |
| **Mutex Type**   | `std::mutex` or `std::shared_mutex` | Only `std::shared_mutex` |
| **Use Case**     | When modifying shared data | When reading shared data |

---

### **Understanding `lock()`, `try_lock()`, and `operator bool()` in C++**  

C++ provides **mutex-related functions** for managing thread synchronization efficiently.  

### **1. `lock()`**
- Blocks the calling thread **until it acquires the lock**.  
- Ensures **exclusive access** to a shared resource.  
- If another thread already holds the lock, `lock()` **waits**.  

#### **Example: Using `lock()`**
```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::mutex mtx;

void task(int id) {
    mtx.lock(); // Acquire lock
    std::cout << "Thread " << id << " is executing...\n";
    std::this_thread::sleep_for(std::chrono::seconds(1)); // Simulate work
    mtx.unlock(); // Release lock
}

int main() {
    std::thread t1(task, 1);
    std::thread t2(task, 2);

    t1.join();
    t2.join();

    return 0;
}
```
#### **Key Points**
✔️ `lock()` ensures only **one thread** executes the critical section at a time.  
✔️ **Blocking function**—the thread waits until it gets the lock.  

---

### **2. `try_lock()`**
- Tries to acquire the lock **without blocking**.  
- If the lock is **available**, it **succeeds** and returns `true`.  
- If the lock is **already taken**, it **fails immediately** and returns `false`.  

#### **Example: Using `try_lock()`**
```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::mutex mtx;

void task(int id) {
    if (mtx.try_lock()) { // Try acquiring the lock
        std::cout << "Thread " << id << " acquired the lock!\n";
        std::this_thread::sleep_for(std::chrono::seconds(1));
        mtx.unlock(); // Release the lock
    } else {
        std::cout << "Thread " << id << " could not acquire the lock!\n";
    }
}

int main() {
    std::thread t1(task, 1);
    std::thread t2(task, 2);

    t1.join();
    t2.join();

    return 0;
}
```
#### **Possible Output**
```
Thread 1 acquired the lock!
Thread 2 could not acquire the lock!
```
#### **Key Points**
    `try_lock()` **does not block**—it returns immediately.  
    Use for **non-blocking** operations where waiting is not desirable.  

---

### **3. `operator bool()`**
- Checks **whether a `std::unique_lock` object holds a lock**.  
- Returns `true` if the lock is acquired, otherwise `false`.  
- Helps **verify if a lock is successfully acquired**.  

#### **Example: Using `operator bool()`**
```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::mutex mtx;

void task() {
    std::unique_lock<std::mutex> lock(mtx, std::try_to_lock); // Try acquiring the lock
    if (lock) { // Checks if the lock is held
        std::cout << "Lock acquired by thread " << std::this_thread::get_id() << "\n";
    } else {
        std::cout << "Thread " << std::this_thread::get_id() << " could not acquire lock\n";
    }
}

int main() {
    std::thread t1(task);
    std::thread t2(task);

    t1.join();
    t2.join();

    return 0;
}
```
#### **Possible Output**
```
Lock acquired by thread 140235678539520
Thread 140235678539528 could not acquire lock
```
#### **Key Points**
    `operator bool()` is used with `std::unique_lock` to check if locking was successful.  
    Helps in **conditional execution** based on lock acquisition.  

---

## **Key Differences Between `lock()`, `try_lock()`, and `operator bool()`**
| Feature         | `lock()` | `try_lock()` | `operator bool()` |
|---------------|---------|------------|----------------|
| **Blocking?** | ✅ Yes | ❌ No (returns immediately) | ❌ No (only checks lock status) |
| **Return Type** | `void` | `bool` (`true` if successful) | `bool` (`true` if lock is held) |
| **Fails If Locked?** | ❌ No (waits for lock) | ✅ Yes (returns `false`) | ✅ Yes (returns `false`) |
| **Use Case** | When waiting for lock is okay | When non-blocking execution is needed | When checking lock ownership |

---

## **When to Use?**
    **Use `lock()`** when **you need to wait** for the lock (critical section).  
    **Use `try_lock()`** when **you don’t want to block** execution.  
    **Use `operator bool()`** to **check if a lock is held** inside `std::unique_lock`.  

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

### 5. Deadlock Prevention or Avoiding

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

**The following example will lead to deadlocks**
```cpp
std::mutex m1, m2, m3;
void threadA() {
// INTENTIONALLY BUGGY
std::unique_lock l1{m1}, l2{m2}, l3{m3};
}
void threadB() {
// INTENTIONALLY BUGGY
std::unique_lock l3{m3}, l2{m2}, l1{m1};
}
```
**Possible deadlock scenario**
   - threadA() acquires locks on m1 and m2
   - threadB() acquires lock on m3
   - threadA() waits for threadB() to release m3
   - threadB() waits for threadA() to release m2

**Example:**
**Deadlocks can be avoided by always locking mutexes in a globally consistent order**
  - Ensures that one thread always “wins”
  - Maintaining a globally consistent locking order requires considerable developer discipline
  - Maintaining a globally consistent locking order may not be possible at all
    
```cpp
std::mutex m1, m2, m3;
void threadA() {
// OK, will not deadlock
std::unique_lock l1{m1}, l2{m2}, l3{m3};
}
void threadB() {
// OK, will not deadlock
std::unique_lock l1{m1}, l2{m2}, l3{m3};
}
```

**Example:**
**Sometimes it is not possible to guarantee a globally consistent order**
  - The std::scoped_lock RAII wrapper can be used to safely lock any number of mutexes
  - Employs a deadlock-avoidance algorithm if required
  - Generally quite inefficient in comparison to std::unique_lock
  - Should only be used as a last resort!
    
```cpp
std::mutex m1, m2, m3;
void threadA() {
// OK, will not deadlock
std::scoped_lock l{m1, m2, m3};
}
void threadB() {
// OK, will not deadlock
std::scoped_lock l{m3, m2, m1};
}
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

## **Understanding Binary Semaphore in C++**

### **What is a Binary Semaphore?**
A **Binary Semaphore** is a synchronization mechanism that can have **only two states**:
1. **0 (Locked/Unavailable)**
2. **1 (Unlocked/Available)**  

- It is similar to a **mutex** but allows signaling between threads.  
- Used for **resource control** when only **one thread** should access a shared resource at a time.  
- Unlike a mutex, **semaphores can be signaled (released) by a different thread** than the one that acquired them.  

---

## **Example: Binary Semaphore Using `std::counting_semaphore` (C++20)**
🔹 **`std::counting_semaphore<1>` works like a binary semaphore** in C++20.  
🔹 **One thread waits until the resource is available, and another thread signals (releases) it.**

```cpp
#include <iostream>
#include <thread>
#include <semaphore> // C++20: Required for std::binary_semaphore

std::counting_semaphore<1> semaphore(1); // Binary semaphore (1 means available)

void task(int id) {
    std::cout << "Thread " << id << " waiting for resource...\n";
    semaphore.acquire(); // Wait (P operation)
    
    std::cout << "Thread " << id << " acquired the resource!\n";
    std::this_thread::sleep_for(std::chrono::seconds(2)); // Simulate work

    std::cout << "Thread " << id << " releasing the resource...\n";
    semaphore.release(); // Signal (V operation)
}

int main() {
    std::thread t1(task, 1);
    std::thread t2(task, 2);

    t1.join();
    t2.join();

    return 0;
}
```

---

## **🔹 Expected Output**
```
Thread 1 waiting for resource...
Thread 1 acquired the resource!
Thread 2 waiting for resource...
Thread 1 releasing the resource...
Thread 2 acquired the resource!
Thread 2 releasing the resource...
```

---

## **🔹 Explanation**
1. **Thread 1 starts and acquires the semaphore** (resource is locked).
2. **Thread 2 waits** since the semaphore is already acquired.
3. **Thread 1 releases the semaphore** after work is done.
4. **Thread 2 acquires the semaphore** and executes.
5. **Thread 2 releases the semaphore** after execution.

---

## **Key Differences Between Binary Semaphore & Mutex**
| Feature         | **Binary Semaphore** | **Mutex** |
|---------------|------------------|--------|
| **Value Range** | 0 or 1 | Only locked/unlocked |
| **Ownership** | Can be released by a different thread | Must be released by the same thread |
| **Blocking?** | Yes, if unavailable | Yes, if locked |
| **Use Case** | Resource synchronization | Mutual exclusion |

---

## **When to Use a Binary Semaphore?**
✔ **Use `std::binary_semaphore` when signaling is needed between threads** (e.g., producer-consumer).  
✔ **Use `std::mutex` when a thread must exclusively access a resource** and release it itself.  

Would you like an example using C++11 (without C++20 `semaphore`)? 🚀

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


