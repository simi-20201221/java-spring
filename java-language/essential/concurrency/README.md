# Java Concurrency
============


## Reference

* [Concurrency -oracle](https://docs.oracle.com/javase/tutorial/essential/concurrency/index.html)
* [Java concurrency - wikipedia](https://en.wikipedia.org/wiki/Java_concurrency)
* [Java Concurrency and Multithreading Tutorial - by Jakob Jenkov](http://tutorials.jenkov.com/java-concurrency/index.html)
* [Java Concurrency - by Baeldung](https://www.baeldung.com/java-concurrency)
* [Java concurrency (multi-threading) Tutorial - by Vogella](https://www.vogella.com/tutorials/JavaConcurrency/article.html)
* [Java Concurrency Tutorial - by HowToDoInJava](https://howtodoinjava.com/java-concurrency-tutorial/)
* [Modern concurrency - manning](https://livebook.manning.com/book/the-well-grounded-java-developer/chapter-4/)


## Book

* Core Java Volume I – Fundamentals
* Java Concurrency in Practice by Brian Goetz


## Course

* TODO

## Content


### Core Java Volume I – Fundamentals

#### Introduction

* Multitasking is operating system's ability to have many program working at what the seems like the same time.
* For example, can print while editing or downloading email.
* The number of concurrently executing processes is not limited by the number of CPUs.
* Multithreaded programs extend the idea of multitasking:
    * Individual programs will do multiple tasks at the same time.
    * Each task is executed in a thread. 
    * Multithreaded programs are the programs that can run more than one thread at once.
* The difference between multiple processes and multiple threads:
    * Each process has a complete set of its own variables, threads share the same data. 
    * Communication between threads more efficient and easier to program than interprocess communication.
    * Threads are more “lightweight” than processes it takes less overhead to create and destroy individual threads than it does to launch new processes.


#### Thread

##### (1). What are Thread?

* For example
    * The program moves money between bank accounts. 
    * Use of a Bank class that stores the balances of a given number of accounts. 
    * The transfer method transfers an amount from one account to another. 
    * The implementation.
        * The first thread, move money from account 0 to account 1. 
        * The second thread, moves money from account 2 to account 3.


##### (2). How to control the interaction between threads.

##### (3). Thread State - Life Cycle

* Threads can be in one of six states:
    * ![]() TODO image Figure 12.1 Thread states
    * New
        * When create a thread with the new operator 
        * For example, new Thread(r) 
        * The thread is not yet running. 
        * The thread is in the new state. 
        * The program has not started executing code inside of it. 
    * Runnable
        * When invoke the start method, the thread is in the runnable state.
        * A runnable thread may or may not actually be running. 
        * It is up to the operating system to give the thread time to run. 
        * Once a thread is running, it doesn’t necessarily keep running. 
        * Thread scheduling.
        * A thread loses control only when it calls the yield method, or when it is blocked or waiting.
        * On a machine with multiple processors, each processor can run a thread, and can have multiple threads run in parallel. 
        * If there are more threads than processors,  the scheduler still has to do time slicing.
        * Always keep in mind that a runnable thread may or may not be running at any given time. (This is why the state is called “runnable” and not “running.”)
    * Blocked and Waiting
        * When a thread is blocked or waiting, 
            * It is temporarily inactive. 
            * It does not execute any code and consumes minimal resources. 
            * It is up to the thread scheduler to reactivate it.
        * When the thread tries to acquire an intrinsic object lock that is currently held by another thread, it becomes blocked. 
            * The thread becomes unblocked when all other threads have relinquished the lock and the thread scheduler has allowed this thread to hold it.
            * When the thread waits for another thread to notify the scheduler of a condition, it enters the waiting state. This happens by calling the Object.wait or Thread.join method, or by waiting for a Lock or Condition in the java.util.concurrent library.
            * Several methods have a timeout parameter. 
                * Calling them causes the thread to enter the timed waiting state. 
                * This state persists either until the timeout expires or the appropriate notification has been received. 
                * Methods with timeout include Thread.sleep and the timed versions of Object.wait, Thread.join, Lock.tryLock, and Condition.await.
        * The states that a thread can have and the possible transitions from one state to another. 
            * When a thread is blocked or waiting (or, of course, when it terminates), another thread will be scheduled to run. 
            * When a thread is reactivated (for example, because its timeout has expired or it has succeeded in acquiring a lock), the scheduler checks to see if it has a higher priority than the currently running threads. If so, it preempts one of the current threads and picks a new thread to run.
    * Timed waiting
    * Terminated
        * A thread is terminated for one of two reasons:
            * It dies a natural death because the run method exits normally.
        * It dies abruptly because the run method terminates by an uncaught exception.
* The getState method to determine the current state of a thread.


##### (4). Thread Property

* Interrupting Threads
    * A thread terminates when its run method returns
        * by executing a return statement, 
        * after executing the last statement in the method body, 
        * or if an exception occurs that is not caught in the method. 
    * The interrupt method can be used to request termination of a thread.
        * When the interrupt method is called on a thread,  the interrupted status of the thread is set. 
        * This is a boolean flag that is present in every thread. 
        * Each thread should occasionally check whether it has been interrupted.
        * To find out whether the interrupted status was set, 
            * first call the static Thread.currentThread method to get the current thread, 
            * and then call the isInterrupted method
        * If a thread is blocked, it cannot check the interrupted status. 
            * When the interrupt method is called on a thread that blocks (such as sleep or wait, the blocking call is terminated by an InterruptedException). 
        * The isInterrupted check is neither necessary nor useful if call the sleep method (or another interruptible method) after every work iteration. 
        * If call the sleep method when the interrupted status is set, 
            * it doesn’t sleep.
            * it clears the status and throws an InterruptedException. 
            * => if loop calls sleep, don’t check the interrupted status. instead, catch the InterruptedException.
    * There are two very similar methods, interrupted and isInterrupted.
        * The interrupted method is a static method that`checks` whether the current thread has been interrupted. And calling the interrupted method clears the interrupted status of the thread. 
        * The isInterrupted method is an instance method that check whether any thread has been interrupted.  And calling it does not change the interrupted status.
    * In the catch clause, 
        * call Thread.currentThread().interrupt() to set the interrupted status. Then the caller can test it.
        * Or, even better, tag method with throws InterruptedException and drop the try block. Then the caller (or, ultimately, the run method) can catch it. 
* Daemon Threads
    * Can turn a thread into a daemon thread by calling t.setDaemon(true);
    * A daemon is simply a thread that has no other role in life than to serve others. 
    * When only daemon threads remain, the virtual machine exits. 
    * There is no point in keeping the program running if all remaining threads are daemons.
* Thread Names
* Handlers for Uncaught Exceptions
    * The run method of a thread cannot throw any checked exceptions, but it can be terminated by an unchecked exception. In that case, the thread dies.
    * There is no catch clause to which the exception can be propagated. Instead, just before the thread dies, the exception is passed to a handler for uncaught exceptions.
    * The handler exception
        * Implements the Thread.UncaughtExceptionHandler interface. 
        * Can install a handler into any thread with the setUncaughtExceptionHandler method. 
        * Can install a default handler for all threads with the
    static method setDefaultUncaughtExceptionHandler of the Thread class. 
        * If don’t install a default handler, the default handler is null.
        * If don’t install a handler for an individual thread, the handler is the thread’s ThreadGroup object.
    * A thread group is a collection of threads that can be managed together.
        * By default, all threads that create belong to the same thread group
        * It is possible to establish other groupings. 
        * Recommend that do not use thread groups in programs.
* Thread Priorities
    * Every thread has a`priority`. 
    * By default, a thread inherits the priority of the thread that constructed it. 
    * Can increase or decrease the priority of any thread with the setPriority method. 
        * MIN_PRIORITY (defined as 1 in the Thread class) and 
        * MAX_PRIORITY (defined as 10). 
        * NORM_PRIORITY is defined as 5.
    * Should not use them


#### Synchronization

* In most practical multithreaded applications, two or more threads need to share access to the same data. 
* What happens if two threads have access to the same object and each calls a method that modifies the state of the object?
    * corrupted objects
    * called a race condition.
* To avoid corruption of shared data by multiple threads, must learn how to synchronize the access. 

##### (1). Example of a Race Condition

##### (2). The Race Condition Explained

##### (3). Lock Objects

* There are two mechanisms for protecting a code block from concurrent access.
    * synchronized keyword
        * automatically provides a lock as well as an associated “condition,”
        * powerful and convenient for most cases that require explicit locking. 
    * ReentrantLock class
        * 
            // A ReentrantLock object.
            myLock.lock();
            try {
                // Critical section
            } finally {
                // Make sure the lock is unlocked even if an exception is throw.
                myLock.unlock();
            }
        * This construct guarantees that only one thread at a time can enter the critical section. 
            * As soon as one thread locks the lock object, no other thread can get past the lock statement. 
            * When other threads call lock, they are deactivated until the first thread unlocks the lock object.
        * It is critically important that the unlock operation is enclosed in a finally clause. 
            * If the code in the critical section throws an exception, the lock must be unlocked. 
            * Otherwise, the other threads will be blocked forever.
        * When use locks, can not use the try-with-resources statement.
            * First off, the unlock method isn’t called close. 
            * Its header expects the declaration of a new variable. But when use a lock, want to keep using the same variable that is shared among threads.

##### (4). Condition Objects

##### (5). The synchronized Keyword

##### (6). Synchronized Blocks

##### (7). The Monitor Concept

##### (8). Volatile Fields

##### (9). Final Variables

##### (10). Atomics

##### (11). Deadlocks

##### (12). Thread-Local Variables

##### (13). Why the stop and suspend Methods Are Deprecated

#### Thread-Safe Collection

#### Task and Thread Pool

#### Asynchronous Computation

#### Process


### Oracle Java 8 Documentation

* Concurrent Programming
    * Basic unit of execution
        * Process
        * Thread
    * A computer system normally has many active `processes` and `threads`.
    * Only have `one thread actually` executing at any given moment. 
    * `Time slicing` is `processing time` for a single core that is shared among processes and threads through an OS feature.
* Processes
    * A process has a self-contained `execution environment`. 
    * A process generally has a complete, private set of basic run-time resources; in particular, each process has its own memory space.
    * Processes are often seen as synonymous with programs or applications. 
    * Most operating systems support `Inter Process Communication (IPC)` resources, such as pipes and sockets, to facilitate communication between processes.
    * Most implementations of the `JVM` run as a `single process`. 
    * A Java application can `create additional processes` using a `ProcessBuilder` object.
* Threads
    * Threads are sometimes called `lightweight` processes.
    * Both processes and threads provide an `execution environment`, but creating a new thread requires fewer resources than creating a new process.
    * `Threads exist within a process — every process has at least one`.
    * Threads share the process's resources, including memory and open files => This makes for efficient, but potentially problematic, communication.
    * Every application has at least one thread, the `application's point` starts with just `one thread` - `main thread`.
    * Each thread is associated with an instance of the class Thread.
    * Basic `strategies` for using `Thread` objects to `create` a `concurrent` application
        * (1). Use of Thread object
            * To directly control thread creation and management, simply instantiate Thread each time the application needs to initiate an asynchronous task.
            * Defining Thread
                * An application that creates an instance of Thread must provide the code that will run in that thread.
                    * Provide a Runnable object.
                        * The Runnable interface defines a single method - run() to contain the code executed in the thread. 
                        * The Runnable object is passed to the Thread constructor.
                        * ```java
                            public class ThreadImplementRunnable implements Runnable {
                                @Override
                                public void run() {
                                    System.out.printf("Thread Implemented from Runnable");
                                }
                            }```
                    * Subclass Thread.
                        * The Thread class itself implements Runnable, though its run method does nothing.
                        * An application can subclass Thread, providing its own implementation of run.
                        * ```java
                            public class ThreadExtendRunnable extends Thread {
                                @Override
                                public void run() {
                                    super.run();
                                    System.out.printf("Thread Extended From Thread");
                                }
                            }```
                        * The Thread class defines a number of methods useful for thread management. 
                            * These include static methods, which provide information about or affect the status of, the thread invoking the method. 
                            * The other methods are invoked from other threads involved in managing the thread and Thread object. 
                * Should Use
                    * Which employs a Runnable object, is more general, because the Runnable object can subclass a class other than Thread.
                    * Easier to use in simple applications, but is limited by the fact that your task class must be a descendant of Thread.
            * Starting Thread
                * `Thread.start`
            * Pausing Execution with Sleep
                * `Thread.sleep` causes the current thread to `suspend` execution for a specified period.
                * This is an efficient means of making processor time available to the other threads of an application or other applications that might be running on a computer system.
                * The sleep method can be used for pacing.
                * Two overloaded versions of sleep
                    * specifies the sleep time to the millisecond
                    * specifies the sleep time to the nanosecond
                * These sleep times are not guaranteed to be precise, because they are limited by the facilities provided by the underlying OS.
                * The sleep period can be terminated by an interruptions. `InterruptedException` is an exception that sleep throws when another thread interrupts the current thread while sleep is active. 
                * In any case, can not assume that invoking sleep will suspend the thread for precisely the time period specified.
                * ```java
                    public class ThreadSleep implements Runnable {
                        @Override
                        public void run() {
                            System.out.printf("Thread Extended From Thread");
                    
                            try {
                                Thread.sleep(5000);
                            } catch (InterruptedException e) {
                                e.printStackTrace();
                            }
                        }
                    }```
            * Interrupts
                * An interrupt is an indication to a thread that it should stop what it is doing and do something else. 
                * It's up to the programmer to decide exactly how a thread responds to an interruption, but it is very common for the thread to terminate.
                * A thread sends an interrupt by invoking interrupt on the Thread object for the thread to be interrupted. 
                * For the interrupt mechanism to work correctly, the interrupted thread must support its own interruption.
                * Supporting Interruption
                    * ```java
                        if (Thread.interrupted()) {
                            throw new InterruptedException();
                        }```
                * Interrupt Status Flag
                    * The interrupt mechanism is implemented using an internal flag known as the `interrupt status`.
                    * When a thread checks for an interrupt by invoking the static method Thread.interrupted, interrupt status is cleared.
                    * The non-static isInterrupted method, which is used by one thread to query the interrupt status of another, does not change the interrupt status flag.
                    * By convention, any method that exits by throwing an InterruptedException clears interrupt status when it does so. However, it's always possible that interrupt status will immediately be set again, by another thread invoking interrupt.
            * Joins
                * The join method allows one thread to wait for the completion of another.
                * ```java 
                    t.join();```
                * If t is a Thread object whose thread is currently executing, causes the current thread to pause execution until t's thread terminates.
                * Overloads of join allow the programmer to specify a waiting period. However, as with sleep, join is dependent on the OS for timing, so you should not assume that join will wait exactly as long as you specify.
                * Like sleep, join responds to an interrupt by exiting with an InterruptedException.
            * Example ```java
                    public class SimpleThread {
                    
                        // Display a message, preceded by the name of the current thread.
                        static void threadMessage(String message) {
                            String threadName = Thread.currentThread().getName();
                    
                            System.out.format("%s: %s%n", threadName, message);
                        }
                    
                        public static void main(String[] args) throws InterruptedException {
                            // Delay, in milliseconds before we interrupt MessageLoop thread (default one hour).
                            long patience = 1000 * 60 * 60;
                    
                            // If command line argument present, gives patience in seconds.
                            if (args.length > 0) {
                                try {
                                    patience = Long.parseLong(args[0]) * 1000;
                                } catch (NumberFormatException e) {
                                    System.err.println("Argument must be an integer.");
                                    System.exit(1);
                                }
                            }
                    
                            threadMessage("Starting MessageLoop thread");
                            final long startTime = System.currentTimeMillis();
                            final Thread t = new Thread(new MessageLoop());
                            t.start();
                    
                            threadMessage("Waiting for MessageLoop thread to finish");
                            // Loop until MessageLoop thread exits.
                            while (t.isAlive()) {
                                threadMessage("Still waiting...");
                                // Wait maximum of 1 second for MessageLoop thread to finish.
                                t.join(1000);
                                if (((System.currentTimeMillis() - startTime) > patience) && t.isAlive()) {
                                    threadMessage("Tired of waiting!");
                                    t.interrupt();
                                    // Shouldn't be long now-- wait indefinitely
                                    t.join();
                                }
                            }
                            threadMessage("Finally!");
                        }
                    
                        public static class MessageLoop implements Runnable {
                            @Override
                            public void run() {
                    
                                final String[] importantInfo = {
                                        "Mares eat oats",
                                        "Does eat oats",
                                        "Little lambs eat ivy",
                                        "A kid will eat ivy too"
                                };
                                try {
                                    for (String s : importantInfo) {
                                        // Pause for 4 seconds
                                        Thread.sleep(4000);
                                        // Print a message
                                        threadMessage(s);
                                    }
                                } catch (InterruptedException e) {
                                    threadMessage("I wasn't done!");
                                }
                            }
                        }
                    }```
        * (2). Hight-level concurrency object
            * To abstract thread management from the rest of your application, pass the application's tasks to an `executor`.
            * `Lock objects` 
                * support locking idioms that simplify many concurrent applications.
                * Synchronized code relies on a simple kind of reentrant lock. This kind of lock is easy to use, but has many limitations.
                * Lock objects work very much like the implicit locks used by synchronized code. As with implicit locks, only one thread can own a Lock object at a time. Lock objects also support a wait/notify mechanism, through their associated Condition objects.
                * The biggest advantage of Lock objects over implicit locks is their ability to back out of an attempt to acquire a lock. The tryLock method backs out if the lock is not available immediately or before a timeout expires (if specified). The lockInterruptibly method backs out if another thread sends an interrupt before the lock is acquired.
                * Let's use Lock objects to solve the deadlock problem in Liveness.
            * `Executors` 
                * define a high-level API for launching and managing threads. 
                * Executor implementations provided by java.util.concurrent provide thread pool management suitable for large-scale applications.
                * Executor Interfaces define the three executor object types. 
                    * `Executor`, 
                        * a simple interface that supports launching new tasks.
                        * The Executor interface provides a single method, execute, designed to be a drop-in replacement for a common thread-creation idiom.
                        * ```java e.execute(r);```
                    * `ExecutorService`, a subinterface of Executor, which adds features that help manage the life cycle, both of the individual tasks and of the executor itself.
                    * `ScheduledExecutorService`, a subinterface of ExecutorService, supports future and/or periodic execution of tasks.
                * Thread Pools are the most common kind of executor implementation.
                * Fork/Join is a framework (new in JDK 7) for taking advantage of multiple processors.
            * `Concurrent` collections 
                * make it easier to manage large collections of data, and can greatly reduce the need for synchronization.
            * `Atomic variables` 
                * have features that minimize synchronization and help avoid memory consistency errors.
            * `ThreadLocalRandom` (in JDK 7) provides efficient generation of pseudo-random numbers from multiple threads.
* Synchronization
    * Threads `communicate` primarily by `sharing access` to `fields` and the `objects reference fields` refer to. 
    * This form of communication is extremely efficient, but makes `two kinds of errors` possible: 
        * thread interference and 
        * memory consistency errors. 
    * The tool needed to prevent these errors is `synchronization`.
    * Synchronization can introduce `thread contention` (which occurs when two or more threads try to access the same resource simultaneously and cause the Java runtime to execute one or more threads more slowly, or even suspend their execution).
    * Liveness
        * A concurrent application's ability to execute in a timely manner is known as its liveness.
        * Deadlock
        * Deadlock describes a situation where two or more threads are blocked forever, waiting for each other. * Example
            * ```java
                public class Deadlock {
                    static class Friend {
                        private final String name;
                        
                        public Friend(String name) {
                            this.name = name;
                        }
                        
                        public String getName() {
                            return this.name;
                        }
                        
                        public synchronized void bow(Friend bower) {
                            System.out.format("%s: %s" + "  has bowed to me!%n", this.name, bower.getName());
                            bower.bowBack(this);
                        }
                        
                        public synchronized void bowBack(Friend bower) {
                            System.out.format("%s: %s" + " has bowed back to me!%n", this.name, bower.getName());
                        }
                    }
                
                    public static void main(String[] args) {
                        final Friend alphonse = new Friend("Alphonse");
                        final Friend gaston = new Friend("Gaston");
                        
                        new Thread(new Runnable() {
                            public void run() { alphonse.bow(gaston); }
                        }).start();
                        
                        new Thread(new Runnable() {
                            public void run() { gaston.bow(alphonse); }
                        }).start();
                    }
                }```
            * Alphonse and Gaston are friends, and great believers in courtesy.
            * A strict rule of courtesy is that when you bow to a friend, you must remain bowed until your friend has a chance to return the bow.
            * Unfortunately, this rule does not account for the possibility that two friends might bow to each other at the same time. 
            * When Deadlock runs, it's extremely likely that both threads will block when they attempt to invoke bowBack. 
            * Neither block will ever end, because each thread is waiting for the other to exit bow.
    * Forms of thread contention
        * Starvation
            * Starvation describes a situation where a thread is unable to gain regular access to shared resources and is unable to make progress. 
            * This happens when shared resources are made unavailable for long periods by "greedy" threads. * For example, suppose an object provides a synchronized method that often takes a long time to return. If one thread invokes this method frequently, other threads that also need frequent synchronized access to the same object will often be blocked.
        * Livelock
            * A thread often acts in response to the action of another thread. 
                * If the other thread's action is also a response to the action of another thread, then livelock may result. 
            * Livelocked threads are unable to make further progress. 
                * The threads are not blocked — they are simply too busy responding to each other to resume work. 
            * For example, 
                * Two people attempting to pass each other in a corridor.
                * Alphonse moves to his left to let Gaston pass, while Gaston moves to his right to let Alphonse pass. 
                * Seeing that they are still blocking each other, Alphone moves to his right, while Gaston moves to his left. 
                * They're still blocking each other, so...
    * `Thread Interference` 
        * describes how errors are introduced when multiple threads access shared data.
        * `Interference` happens when two operations, running in different threads, but acting on the same data, interleave. This means that the two operations consist of multiple steps, and the sequences of steps overlap.
        * Example
            * ```java
                public class Counter {
                    private int c = 0;
                
                    public void increment() {
                        c++;
                    }
                
                    public void decrement() {
                        c--;
                    }
                
                    public int value() {
                        return c;
                    }
                }```
            * Counter is designed so that each invocation of increment will add 1 to c, and each invocation of decrement will subtract 1 from c.
            * If a Counter object is referenced from multiple threads, interference between threads may prevent this from happening as expected.
            * Suppose Thread A invokes increment at about the same time Thread B invokes decrement. If the initial value of c is 0, their interleaved actions might follow this sequence:
                * (1) Thread A: Retrieve c.
                * (2) Thread B: Retrieve c.
                * (3)Thread A: Increment retrieved value; result is 1.
                * (4) Thread B: Decrement retrieved value; result is -1.
                * (5) Thread A: Store result in c; c is now 1.
                * (6) Thread B: Store result in c; c is now -1.
            * Thread A's result is lost, overwritten by Thread B. This particular interleaving is only one possibility. Under different circumstances it might be Thread B's result that gets lost, or there could be no error at all. Because they are unpredictable, thread interference bugs can be difficult to detect and fix.
    * `Memory Consistency Errors` 
        * describes errors that result from inconsistent views of shared memory.
        * occur when different threads have inconsistent views of what should be the same data.
        * The key to avoiding memory consistency errors is understanding the `happens-before` relationship.
            * This relationship is simply a guarantee that memory writes by one specific statement are visible to another specific statement. 
            * Example
                * ```java
                    // Suppose a simple int field is defined and initialized.
                    int counter = 0;
                
                    // The counter field is shared between two threads, A and B. Suppose thread A increments counter.
                    counter++;
                
                    // Then, shortly afterwards, thread B prints out counter.
                    System.out.println(counter);```
                * If the two statements had been executed in the same thread, it would be safe to assume that the value printed out would be "1".
                * But if the two statements are executed in separate threads, the value printed out might well be "0", because there's no guarantee that thread A's change to counter will be visible to thread B — unless the programmer has established a happens-before relationship between these two statements.
        * [List of actions that create happens-before relationships](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/package-summary.html#MemoryVisibility)
    * Basic synchronization idioms
        * `synchronized methods`
            * describes a simple idiom that can effectively prevent thread interference and memory consistency errors.
            * `synchronized` keyword to make a method synchronized
            * Example
                * ```java
                    public class SynchronizedCounter {
                        private int c = 0;
                    
                        public synchronized void increment() {
                            c++;
                        }
                    
                        public synchronized void decrement() {
                            c--;
                        }
                    
                        public synchronized int value() {
                            return c;
                        }
                    }```
                * If count is an instance of SynchronizedCounter, then making these methods synchronized has two effects:
                    * First, it is not possible for two invocations of synchronized methods on the same object to interleave. When one thread is executing a synchronized method for an object, all other threads that invoke synchronized methods for the same object block (suspend execution) until the first thread is done with the object.
                    * Second, when a synchronized method exits, it automatically establishes a happens-before relationship with any subsequent invocation of a synchronized method for the same object. This guarantees that changes to the state of the object are visible to all threads.
            * `Constructors can not be synchronized`
                * using the synchronized keyword with a constructor is a `syntax error`. 
                * Synchronizing constructors doesn't make sense, because only the thread that creates an object should have access to it while it is being constructed.
            * When constructing an object that will be shared between threads, be very careful that a reference to the object does not `leak` prematurely.
            * Synchronized methods enable a simple strategy for preventing thread interference and memory consistency errors: 
                *  if an object is visible to more than one thread, all reads or writes to that object's variables are done through synchronized methods.
                *  An important exception: final fields, which cannot be modified after the object is constructed, can be safely read through non-synchronized methods, once the object is constructed.
                *  This strategy is effective, but can present problems with `liveness` 
        * `synchronized statements`
            * synchronized statements must specify the object that provides the intrinsic lock:
                * ```java
                    public void addName(String name) {
                        synchronized(this) {
                            lastName = name;
                            nameCount++;
                        }
                        nameList.add(name);
                    }```
                * the addName method needs to synchronize changes to lastName and nameCount, but also needs to avoid synchronizing invocations of other objects' methods. 
                * Without synchronized statements, there would have to be a separate, unsynchronized method for the sole purpose of invoking nameList.add.
            * Synchronized statements are also useful for improving concurrency with fine-grained synchronization.
                * ```java
                    public class MsLunch {
                        private long c1 = 0;
                        private long c2 = 0;
                        private Object lock1 = new Object();
                        private Object lock2 = new Object();
                    
                        public void inc1() {
                            synchronized(lock1) {
                                c1++;
                            }
                        }
                    
                        public void inc2() {
                            synchronized(lock2) {
                                c2++;
                            }
                        }
                    }```
                * class MsLunch has two instance fields, c1 and c2, that are never used together.
                *  All updates of these fields must be synchronized, but there's no reason to prevent an update of c1 from being interleaved with an update of c2 — and doing so reduces concurrency by creating unnecessary blocking.
                * Instead of using synchronized methods or otherwise using the lock associated with this, create two objects solely to provide locks.
    * `Implicit Locks` and `Synchronization` 
        * describes a more general synchronization idiom, and describes how synchronization is based on implicit locks.
        * Synchronization is built around an internal entity known as the `intrinsic lock` or `monitor lock`. 
        * Intrinsic locks play a role in both aspects of synchronization: 
            * enforcing exclusive access to an object's state and 
            * establishing happens-before relationships that are essential to visibility.
        * Every object has an intrinsic lock associated with it.
            * By convention, a thread that needs exclusive and consistent access to an object's fields has to acquire the object's intrinsic lock before accessing them, and then release the intrinsic lock when it's done with them. 
            * A thread is said to own the intrinsic lock between the time it has acquired the lock and released the lock. As long as a thread owns an intrinsic lock, no other thread can acquire the same lock.
            * The other thread will block when it attempts to acquire the lock.
            * When a thread releases an intrinsic lock, a happens-before relationship is established between that action and any subsequent acquisition of the same lock.
        * Locks In Synchronized Methods
            * When a thread invokes a synchronized method, it automatically acquires the intrinsic lock for that method's object and releases it when the method returns. The lock release occurs even if the return was caused by an uncaught exception.
            * A `static method` is associated with a class, not an object. 
                * In this case, the thread acquires the intrinsic lock for the Class object associated with the class. 
                * Thus access to class's static fields is controlled by a lock that's distinct from the lock for any instance of the class.
        * Reentrant Synchronization
            * A thread cannot acquire a lock owned by another thread, but a thread can acquire a lock that it already owns.
            * Allowing a thread to acquire the same lock more than once enables reentrant synchronization.
            * This describes a situation where synchronized code, directly or indirectly, invokes a method that also contains synchronized code, and both sets of code use the same lock. 
            * Without reentrant synchronization, synchronized code would have to take many additional precautions to avoid having a thread cause itself to block.
    * `Atomic Access`
        * talks about the general idea of operations that can't be interfered with by other threads.
        * In programming, an `atomic` action is one that effectively happens all at once. 
        * An atomic action cannot stop in the middle: it either happens completely, or it doesn't happen at all. 
        * No side effects of an atomic action are visible until the action is complete.
        * Actions can specify that are atomic
            * Reads and writes are atomic for reference variables and for most primitive variables (all types except long and double).
            * Reads and writes are atomic for all variables declared `volatile` (including long and double variables).
        * Atomic actions cannot be interleaved, so they can be used without fear of thread interference, but memory consistency errors are still possible. 
        * Using volatile variables reduces the risk of memory consistency errors, because any write to a volatile variable establishes a happens-before relationship with subsequent reads of that same variable.
            * This means that changes to a volatile variable are always visible to other threads.
            * What's more, it also means that when a thread reads a volatile variable, it sees not just the latest change to the volatile, but also the side effects of the code that led up the change.
        * Using simple atomic variable access is more efficient than accessing these variables through synchronized code, but requires more care by the programmer to avoid memory consistency errors. Whether the extra effort is worthwhile depends on the size and complexity of the application.
* Guarded Blocks
    * https://docs.oracle.com/javase/tutorial/essential/concurrency/guardmeth.html
* Immutable Objects
    * An object is considered immutable if its state cannot change after it is constructed. 
    * Immutable objects are particularly useful in concurrent applications, because they cannot change state, they cannot be corrupted by thread interference or observed in an inconsistent state.
    * Rules define a simple strategy for creating immutable objects. 
        * (1) Don't provide "setter" methods — methods that modify fields or objects referred to by fields.
        * (2) Make all fields final and private.
        * (3) Don't allow subclasses to override methods. 
            * The simplest way to do this is to declare the class as final. 
            * A more sophisticated approach is to make the constructor private and construct instances in factory methods.
        * If the instance fields include references to mutable objects, don't allow those objects to be changed.
            * Don't provide methods that modify the mutable objects.
            * Don't share references to the mutable objects. Never store references to external, mutable objects passed to the constructor; if necessary, create copies, and store references to the copies. Similarly, create copies of your internal mutable objects when necessary to avoid returning the originals in your methods.

