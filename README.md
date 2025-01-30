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

- **`join()`**: Waits for the thread to finish execution.
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


