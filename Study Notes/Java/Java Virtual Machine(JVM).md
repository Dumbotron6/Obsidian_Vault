The JVM (Java Virtual Machine) is the engine that drives Java's "Write Once, Run Anywhere" capability. It acts as a translator, taking the compiled `.class` bytecode and executing it on the underlying hardware environment.
- Java source (.java) -> compiled by javac -> bytecode (.class).
- JVM loads the bytecode, verifies it, links it, and then executes it.
- Execution may involve interpreting bytecode or using Just-In-Time (JIT) compilation to convert “hot” code into native machine code for performance.
- Garbage collection runs in the background to reclaim memory from unused objects.

### Architecture
![[Java Virtual Machine(JVM).webp]]

#### 1. Class Loader Subsystem

This subsystem is responsible for bringing the compiled bytecode into the JVM. It operates in three distinct phases:

- **Loading:** Reads the `.class` files, generates the corresponding binary data, and creates a `Class` object in the heap.
- **Linking (Preparation):** Prepares the class for execution by verifying the bytecode for security, allocating memory for static variables (Preparation), and replacing symbolic memory references with direct references (Resolution). More on this below.
- **Initialization:** Assigns the actual defined values to static variables and executes any static blocks.
	- During the earlier _Linking (Preparation)_ phase, static variables were allocated memory and given default values (e.g., `0` for integers, `null` for objects). During _Initialization_, the JVM assigns the actual values you specified.
    - _Example:_ If you wrote `static int count = 50;`, during Linking it was `0`. During Initialization, it becomes `50`.
	- *Executes Static Blocks*: Any `static { ... }` blocks defined in the class are executed. This is often used for complex static variable initialization or loading native libraries.
	- The JVM is lazy. It does not initialize a class just because it was loaded. Initialization is strictly delayed until the class is actively used for the first time, like initializing an object of that class, call a static variable or method, a child class is initialized, which will cause the parent to be initialized.

![[Java Virtual Machine(JVM)-1.webp]]

##### Class Loader Types

- ***Bootstrap Class Loader:*** Loads core Java classes (JAVA_HOME/lib) like _java.lang.Object_ into memory.
- ***Extension Class Loader:*** Loads classes from extensions directory (JAVA_HOME/jre/lib/ext). In non-Maven or non-Gradle based applications, where a developer adds JARs manually, all those classes are loaded during this phase.
- ***System/Application Class Loader:*** Loads classes from the application classpath.

The loaders use a strict "Parent Delegation Model." When a class needs to be loaded, the Application loader doesn't load it immediately; it delegates the request up to the Extension loader, which delegates it to the Bootstrap loader. Only if the parent cannot find the class does the child attempt to load it. This security mechanism prevents custom application code from overriding core Java API classes (like `java.lang.String`).

#### 2. Runtime Data Areas (JVM Memory)

Once classes are loaded, the JVM allocates memory to manage the program's execution.

**Thread-Isolated Areas (Created per thread):**
- **Stack Area:** Stores method calls, local variables, and partial results. Each method invocation creates a new "stack frame." Stores class-level information like class name, parent class, methods, variables, and static data.
- **PC Registers:** Holds the exact memory address of the JVM instruction currently being executed by that specific thread.
- **Native Method Stack:** Similar to the standard stack, but specifically for executing native (C/C++) methods.

**Shared Areas (Accessible by all threads):**
- **Heap Area:** The runtime data area where all objects and their corresponding instance variables are allocated.
- **Method Area:** Stores class-level structures like metadata, method code, and the runtime constant pool.

> [!NOTE]
> Since Java 8, the Method Area is implemented as **Metaspace**. Unlike the older "PermGen" (Permanent Generation) which lived inside the JVM heap and frequently caused `OutOfMemoryError: PermGen space` crashes, Metaspace resides in the host machine's _native OS memory_. It auto-scales dynamically, making modern Java applications much more stable.

####  3. Execution Engine

This is the core component that actually runs the loaded and stored bytecode. More on this below.

- **Interpreter:** Reads and executes the bytecode instruction by instruction. The disadvantage here is that when one method is called multiple times, every time interpretation is required. This is where JIT is useful.
- **Just-In-Time (JIT) Compiler:** Identifies frequently executed ("hot") methods and compiles them directly down to native machine code to dramatically speed up execution. It compiles the entire bytecode and changes it to native code so whenever the interpreter sees repeated ("hot") method calls, JIT provides direct native code for that part so re-interpretation is not required, thus efficiency is improved.
- **Garbage Collector (GC):** A background daemon that tracks live objects and destroys unreferenced objects to reclaim heap memory.

#### 4. JNI (Java Native Interface) & Native Method Libraries

- **JNI:** A bridge framework that allows Java code running inside the JVM to interact with applications and libraries written in other languages, such as C and C++.
- **Native Method Libraries:** The actual collection of system-specific C/C++ files that the JNI interacts with to perform hardware-level or OS-level tasks.

### Stack and Heap

The **Heap** is the largest and most dynamic memory area inside the JVM. While the Stack is used for execution (keeping track of method calls and local variables), the Heap is used strictly for **storage**.

It is created when the JVM starts up and is shared across all active threads in your application. Here is exactly what is stored inside the Heap, how it works, and how it is structured.

#### 1. What is Stored in the Heap?

Whenever you use the `new` keyword in Java, the JVM allocates memory in the Heap. Specifically, it stores:

- **Object Instances:** The actual physical data of an object. For example, if you create a `new User("Alice", 25)`, the memory holding the name "Alice" and the integer 25 is placed in the Heap.
- **Instance Variables (Class Fields):** Any non-static variables declared inside a class belong to the object instance and live in the Heap with it. (Static variables, on the other hand, live in the Metaspace).
- **Arrays:** In Java, arrays are treated as objects. Even if you create an array of primitives like `int[] numbers = new int[10];`, the actual array and its 10 integer slots are stored in the Heap.
- **The String Pool:** Since Java 7, a special caching area called the String Constant Pool was moved into the Heap. This pool stores unique String literals to save memory.

#### 2. Heap vs. Stack (The Crucial Difference)

To truly understand the Heap, you have to look at how it interacts with the Stack. Imagine you write this line of code inside a method: `Customer myCustomer = new Customer();`

Here is how the JVM splits this:
1. **`new Customer()` (The Object):** The JVM creates the actual `Customer` object with all its data and places it in the **Heap**.
2. **`myCustomer` (The Reference):** The JVM creates a local variable named `myCustomer` and places it on the **Stack**. This variable simply holds the _direct memory address_ pointing to the object in the Heap.

Because the Heap is shared among all threads, multiple threads can have variables on their individual Stacks that point to the exact same object in the Heap.

#### 3. How the Heap is Structured (Setting up for Garbage Collection)

Garbage Collection's (GC) entire job revolves around the Heap. Because objects take up space, the Heap would quickly run out of memory (throwing an `OutOfMemoryError`) if it wasn't cleaned up.

To make Garbage Collection highly efficient, the JVM logically divides the Heap into smaller sections called **Generations**:

- **Young Generation (Eden Space & Survivor Spaces):** Every time you create a brand-new object, it is born here. Most objects in Java are short-lived (like temporary variables inside a `for` loop). The GC scans this area very frequently and quickly destroys objects that are no longer being used.
- **Old Generation (Tenured Space):** If an object survives multiple rounds of Garbage Collection in the Young Generation (meaning your program is holding onto it for a long time, like a database connection or a cached list), it gets promoted to the Old Generation. The GC scans this area much less frequently to save processing power.

### JIT (Just-In-Time) Compiler

Once class-loading (see above) is complete, all important classes (used at the time of process start) are pushed into the JVM cache (stored as native code) – which makes them accessible faster during runtime. Other classes are loaded on a per-request basis which is *why the initial request to a web application (like an API request) might be slow*.

If the Interpreter is a translator reading a book line-by-line, the JIT compiler is an analyst monitoring which paragraphs are read the most, translating those specific paragraphs into native machine code (stored in JVM cache), and then rewriting the text to make it read faster.

#### 1. The Detection Phase: Profiling and "Hotness"

The JVM doesn't just guess what code to compile; it uses **profiling**. As the Interpreter runs your bytecode, it actively monitors the program's behavior using hidden counters:

- **Method Entry Counter:** Tracks how many times a specific method is called.
- **Back-Edge Counter:** Tracks how many times a loop executes (useful for detecting a loop that runs millions of times inside a method that was only called once).

When these counters cross a specific threshold, the JVM flags that block of code as "hot" and schedules it for JIT compilation.

#### 2. Tiered Compilation (The C1 and C2 Compilers)

Modern JVMs (specifically the HotSpot JVM) do not have just one JIT compiler; they have two, working in a tiered system to balance startup time with peak performance.

- **Tier 0 (Interpreter):** The JVM starts here. Execution is slow, but startup is instant because no compilation time is required.
- **Tier 1, 2, 3 (C1 / Client Compiler):** When code gets slightly warm, the C1 compiler steps in. It compiles the bytecode into native code very quickly, but it only applies lightweight, basic optimizations.
- **Tier 4 (C2 / Server Compiler):** When code gets incredibly hot, the C2 compiler takes over. It compiles slowly and consumes more CPU/memory, but it applies highly aggressive, heavy-duty optimizations. This is where Java achieves its massive performance gains for long-running applications.

#### 3. JIT Optimizations

When the C2 compiler optimizes your code, it actually rewrites it at the machine level. Here are three of the most powerful optimizations it performs:

##### A. Method Inlining
Method calls are computationally expensive (involving pushing and popping frames on the Stack). If you have a small method that is called frequently (like a getter), JIT will completely remove the method call and copy the actual body of the method directly into the calling code.

- _Before JIT:_ `int total = calculateTax(price);`
- _After JIT:_ `int total = price * 0.20;` (The method call disappears entirely).

##### B. Escape Analysis (Stack Allocation)
Objects are normally created in the Heap (see above). However, Heap allocation requires Garbage Collection, which is expensive. JIT performs "Escape Analysis" to see if an object ever "escapes" the method it was created in (e.g., by being returned or assigned to a global variable). If JIT determines the object is strictly used locally inside the method, **it will allocate the object on the Stack instead of the Heap**. When the method ends, the Stack frame pops, and the object is destroyed instantly—zero Garbage Collection required.

##### C. Loop Unrolling
If you have a `for` loop that iterates 1,000 times, the CPU has to evaluate the loop condition (`i < 1000`) and jump back to the start 1,000 times. JIT will "unroll" the loop by repeating the loop body multiple times in a row, significantly reducing the overhead of evaluating the loop condition and keeping the CPU pipeline full.

#### 4. Deoptimization (The Fallback)

Because Java is highly dynamic (classes can be loaded at runtime, polymorphism allows different object types to be passed), the aggressive assumptions made by the C2 compiler might suddenly become false.

For example, JIT might inline a method assuming there is only one implementation of an interface. If your application suddenly loads a new class that implements that same interface, JIT's assumption is broken.

When this happens, the JVM performs a **Deoptimization**. It pauses execution, throws away the heavily optimized native code, and falls gracefully back to the Tier 0 Interpreter until it can re-profile and re-compile the new code paths. This safety net allows JIT to make aggressive, optimistic optimizations without risking application crashes.

#### 5. C1 vs C2

**C2 optimizes for maximum _execution_ speed, while C1 optimizes for maximum _compilation_ speed.** When code first becomes "warm," the JVM doesn't want to wait for C2 to finish its expensive, CPU-heavy analysis. Instead, it hands the code to C1, which applies lightweight optimizations to get the code out of the slow Interpreter and into native machine code as quickly as possible.

While C1 compiles the bytecode into native code, it simultaneously inserts "profiling hooks" into that native code. As this C1-compiled code runs, it actively records runtime telemetry, such as:

- Which branches of an `if/else` statement are actually taken most of the time?
- Are the objects passed into a method always of the exact same class type?
- Are certain exceptions ever actually thrown?

C1 feeds this data into a Method Data Object (MDO). When the code becomes "hot" enough to trigger C2, C2 uses C1's gathered intelligence to make those aggressive, optimistic optimizations (like Escape Analysis).

Because C1 needs to be fast, it avoids complex, time-consuming algorithms. However, it still applies several highly effective, localized optimizations:

- **Trivial Method Inlining:** While C2 will inline complex and deeply nested methods, C1 will only inline very simple, "trivial" methods—like basic getters and setters or methods with only a few lines of bytecode.
- **Local Value Numbering (Redundancy Elimination):** C1 looks at small blocks of code and removes redundant calculations. For example, if you have `x = a + b` and then two lines later `y = a + b` (and neither `a` nor `b` have changed), C1 will remove the second calculation and just reuse the result of the first.
- **Basic Null Check Elimination:** In Java, every time you access an object, the JVM implicitly checks if it is null to throw a `NullPointerException`. C1 can look at a local block of code and say, "I just saw this object get instantiated" or "I just checked this two lines ago," and strip out the redundant null checks to save CPU cycles.
- **Dead Code Elimination (Basic):** If C1 detects a block of code that mathematically or logically can never be reached (e.g., `if (false) { ... }`), it simply will not compile it into the native machine code.

### FAQ regarding JVM
#### 1. JIT "Warm-up" and Cold Starts

In modern microservice architectures, instances frequently spin up and scale down based on traffic.

- **The Interview Angle:** An interviewer might ask, "Why is our new service instance responding slowly for the first few minutes after deployment?"
- **The Answer:** Because of Tiered Compilation. When a JVM starts, the code runs in the slow Tier 0 Interpreter. It takes time and traffic for the JVM to profile the code, identify the "hot" paths, and let the C1 and C2 compilers generate highly optimized native machine code. This period is called **JVM warm-up**.
- **The Solution:** You might discuss running warm-up scripts before routing live traffic to a new instance, or using pre-warmed container images.

JVM warm up involves running a part of the code in a loop/calling it several times so that JIT considers it "hot".
Link: https://www.baeldung.com/java-jvm-warmup

#### 2. JIT vs. AOT (Ahead-of-Time) Compilation

With the rise of containerization and serverless architectures, JIT's slow startup time can be a liability.

- **Question:** "Have you heard of GraalVM, and why might we choose it over a standard JVM for our microservices?"
- **The Answer:** AOT compilers (like GraalVM) compile the entire Java application directly into OS-specific native machine code _before_ the application ever runs.
- **The Trade-off:** AOT gives you instant startup times and lower memory usage (no JIT compiler or profiling overhead needed at runtime), which is fantastic for reactive stacks and microservices. However, you lose JIT's dynamic runtime optimizations (like aggressive method inlining based on actual live traffic), meaning AOT's peak throughput is sometimes lower than a fully warmed-up JIT.

#### 3. Memory Leaks

Even though the JVM has Garbage Collection, Java applications can absolutely still leak memory.

- **Question:** "If the Garbage Collector cleans up unreferenced objects, how can a Java application still run out of Heap memory?"
- **The Answer:** By holding onto **unintentional references**. If a long-living object (like a static `HashMap`, a `ThreadLocal` variable, or an unclosed database connection) holds a reference to a temporary object, the GC considers that object "alive" and will never destroy it. Over time, the Heap fills up and throws an `OutOfMemoryError: Java heap space`.

#### 4. Metaspace Exhaustion

- **Question:** "What causes an `OutOfMemoryError: Metaspace`, and how is it different from a Heap OOM?"
- **The Answer:** Metaspace holds class metadata, not object instances. A Metaspace OOM usually happens when an application dynamically generates and loads too many new classes at runtime. This is common when using heavy reflection, dynamic proxies (often used by frameworks like Spring), or custom class loaders that fail to unload properly.