* **JVM (Java Virtual Machine):** JVM is the engine that executes Java programs. It converts Java bytecode into machine code that the operating system can understand ([[Java Virtual Machine(JVM)|more]]).
* **JRE (Java Runtime Environment):** JRE provides the environment required to run Java applications. It includes JVM and the necessary libraries and supporting files.
* **JDK (Java Development Kit):** JDK is used for developing Java applications. It includes JRE along with development tools such as the Java compiler and debugger.

What happens when a Java program is executed:
- Write code in a file like HelloWorld.java.
- Java Compiler "javac" compiles it into bytecode "HelloWorld.class".
- JVM reads the .class file and interprets the bytecode.
- The JVM initially interprets bytecode and uses JIT (Just-In-Time) compilation to convert frequently executed code into native machine code for better performance.
- The machine code given by the JVM is then executed by the machine.

## How Does JVM Handle an Exception?

When an Exception occurs, the JVM creates an exception object containing the error name, description, and program state. Throwing an exception means creating an exception object and transferring control to the nearest appropriate exception handler (a catch block) using the throw keyword. There might be a list of the methods that had been called to get to the method where an exception occurred. This ordered list of methods is called call stack. Now the following procedure will happen:

- The run-time system searches the call stack for an exception handler
- It starts searching from the method where the exception occurred and proceeds backward through the call stack.
- If a handler is found, the exception is passed to it.
- If no handler is found, the default exception handler terminates the program and prints the stack trace.

### Built-in Exception

Built-in Exception are pre-defined exception classes provided by Java to handle common errors during program execution. There are two type of built-in exception in java.

- ***Checked Exception:*** These exceptions are checked at compile time, forcing the programmer to handle them explicitly. Ex. ClassNotFounException, FileNotFoundException.
- ***Unchecked Exception:*** These exceptions are checked at runtime and do not require explicit handling at compile time. Ex. NullPointerException, ArrayIndexOutOfBoundsException.

### Exception vs Error

In Java, all exceptions and errors are subclasses of the Throwable class. It has two main branches: Exception and Error.

| Feature     | Exception                                                                                                    | Error                                                                                     |
| ----------- | ------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| Definition  | An event that occurs during program execution, disrupting normal flow, which can be handled using try-catch. | A serious problem that occurs in the JVM, generally cannot be handled by the application. |
| Package     | java.lang.Exception                                                                                          | java.lang.Error                                                                           |
| Recoverable | Yes, can be caught and handled.                                                                              | No, usually not recoverable.                                                              |
| Examples    | IOException, SQLException, ArithmeticException                                                               | OutOfMemoryError, StackOverflowError                                                      |

> [!NOTE]
> Using Optional classes, we can avoid null pointer exceptions more gracefully.
> In Java 8, Optional class was introduced as a container object which may or may not contain a non-null value. It helps avoid NullPointerException by forcing to explicitly handle the case when a value is absent.
> 
> ``` java
> public static void main(String[] args) {
>     Optional<String> name = Optional.ofNullable(null);
> 
>     // Safe way to access
>     System.out.println(name.orElse("Default Name")); // prints: Default Name
> }
> ```

