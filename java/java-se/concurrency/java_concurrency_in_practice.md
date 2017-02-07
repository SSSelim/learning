# Java Concurrency in Practice

- by Brian Goetz and others, Addison Wesley Professional, 2006

- Processes could communicate with one another through a variety of coarse-grained
communication mechanisms: sockets, signal handlers, shared memory, semaphores, and files.

# Several motivating factors of having processes
- Resource utilization - IO waiting
- Fairness - time slicing
- Convenience - easier to write single-task programs 

- waiting for the water to boil involves a degree of asynchrony.(Asynchronous Task)
  While water is heating, you have a choice of what to do.

- Threads come into the pictures because of the same reasons of having processes.

- Each thread has its own program counter, stack, and local variables.

- Since threads share the same memory address space of their owning process,
all threads within a process have access to the same variables and allocate objects
from the same heap, which allows finer-grained data sharing than inter-process mechanisms.

# Benefits of threads
- reduce development and maintenance cost
- improve the performance of complex applications
- improve the responsiveness(GUI)
- resource utilization and throughput(server)

- Since the basic unit of scheduling is the thread, a program with only one
thread can run on at most one processor at a time. On a two-processor system,
a single-threaded program is giving up access to half the available CPU resources.

# Thread Safe Class - manages their own thread
- Timer
- Servlet/JSP
- RMI - lets you invoke methods on objects running in another JVM.
- Swing and AWT

# Part 1 - Fundamentals

## Chapter 2 - Thread Safety
- Writing thread-safe code is,at its core, about managing access to 'state',
and in particular to 'shared', 'mutable state'.

- We may talk about thread safety as if it were about 'code', but what we are
really trying to do is protect 'data' from uncontrolled concurrent access.

- Whether an object needs to be thread-safe depends on whether it will be accessed
from multiple threads. This is a property of how the object is 'used' in a program,
not what it 'does'.

- Whenever more than one thread accesses a given state variable,
and one of them might write to it, they all must coordinate their access to it using synchronization.

- The primary mechanism for snychronization in Java is the "synchronized" keyword,
which provides exclusive locking, but the term "synchronization" also includes
the use of 'volatile' variable, explicit locks, and atomic variables.

- It is far easier to design a class to be thread-safe than to retrofit it for thread safety later.

- Thread safety may be a term that is applied to 'code', but it is about the 'state',
and it can only be applied to the entire body of code that encapsulates its state,
which may be an object or an entire program.

### What is thread safety?
- a class is thread-safe when it continues to behave correctly
  when accessed from multiple threads.

- Stateles : it has no fields and references no fields from other classes.
The transient state for a particular computation exists solely on local variables
that are stored on the thread's stack and are accessible only to the executin thread.

- Since the actions of a thread accessing a stateless object cannot affect
the correctness of operations in other threads, stateless objects are thread-safe.

### Atomicity
- count++ seems to be like a single action because of its compact syntax,
it is not 'atomic', which means that it does not execute as a single, indivisible operation.
This is an example of a read-modify-write operation. This operation is not thread-safe
cus during that three operation, another thread can get in the way, resulting in loss of value.

### Race conditions
A race condition occurs when the correctness of a computation depends on 
relative timing or interleaving of multiple threads by the runtime;
in other words, when getting the right answer relies on the lucky timing.

The most common type of race condition is 'check-then-act', where a potentially
stale observation is used to make a decision on what to do next.

Analogy: Two Starbucks, two friends, meeting, waiting at different starbuck,
then both at the same time checking the other one if the other friend is there.

### Compound Actions
sequences of operations that must be executed atomucally in order to remain thread-safe.

To ensure thread safety, check-then-act operations(like lazy initialization)
and read-modify-write operations (like increment) must always be atomic.

count.incrementAndGet();
java.util.concurrent.atomic package contains 'atomic' variable classes.

### Locking
Adding more than one thread-safe object into stateless object does not guarantee
that our final object is thread safe. Thread-safe field object may be changed
after checking equality. 

### Intrinsic locks
Every Java object can implicitly act as a lock for purposes of synchronization;
these built-in locks are called 'intrinsic locks' or 'monitor locks'.

Intrinsic locks in Java acts as 'mutexes' (or mutual exclusion locks), 
which means that at most one thread may own the lock.

### Reentrancy
When a thread requests a lock that is already held by another thread, 
the requesting thread blocks. But because intrinsic locks are 'reentrant',
if a thread tries to acquire a lock that it already holds, the request succeeds.

Reentrancy means that locks are acquired on a per-thread rather than per-invocation basis.

Without reentrant locks, a snychronized method, in which a subclass overrides
a synchronized method and then calls the superclass method, would deadlock.
If intrinsic locks were not reentrant, the call to super.method would never be able 
to acquire the lock because it would be considered already held, 
and the thread would permanently stall waiting for a lock it can never acquire.

Reentrancy saves us from deadlock in situations like this.

!! Reentrancy is implemented by associating with each lock an acquisition count
and owning thread. When the count is zero, the lock is considered unheld.
When a thread acquires a previously unheld lock, the JVM records the owner 
and sets the acquisition count to one. If that same thread acquires the lock again,
the count is incremented, and when the owning thread exits the synchronized block,
the count is decremented. When the count reaches zero, the lock is released.

### Guarding state with locks
Synchronized block should NOT be too long neither too short.
There should nt be compute-instensive operations, causes performance.
Block shouldnt be too short or too many, create performance overhead.

## Chapter 3 - Sharing Objects
We have seen how synchronized blocks and methods can ensure that operations
execute atomically, but it is a common misconception that 
synchronized  is 'only' about atomicity or demarcating "critical sections".
Synchronization also has another significant, and subtle, aspect: 'memory visibility'.
We want not only to prevent one thread from modifying the state of an object
when another is using it, but also to ensure that when a thread modifies the state of the object,
other threads can actually 'see' the changes that were made.

### Visibility
There is no guarantee about ordering of the statements, 
or visibility of fields, they can be made visible in different order than writing order.
Use proper synchronization whenever data is shared across threads.

### Stale Data
Stale data can cause serious and confusing failures such as unexpected exceptions,
corrupted data structures, inaccurate computations, and infinite loops.

### Nonatomic 64 bit operations
long and double types. Declare them as volatile or guard them by "this".

### Locking and visibility
When a lock is acquired, all changes before the lock are visible.
Without synchronization, there is no such guarantee.

### Volatile variables
Java also provides an alternative, weaker form of synchronization, volatile variables,
to ensure that updates to a variable are propagated predictably to other threads.
When a field is declared 'volative', the compiler and runtime are put on notice
that this variable is shared and that operations on it should not be reordered with other memory operations.
Volatile variables are not cached in regesters or in caches where they are hidden from other processors,
so a read of volatile variable always returns the most recent write by any thread.

### Publication and escape
When a class returns the reference of one its fields, or holds it in a public static field,
this means that references is escaped. 

public static Set<Secret> knownSecrets;

They will also have access to secret object, too.

### Thread Confinement
Accessing shared, mutable data requires using synchronization; 
one way to avoid this requirement is to 'not share'. If data is only accessed from a single thread,
no synchronization is needed. This technique, 'thread confinement', is one of the simplest ways to achieve thread safety.

### Stack confinement
an object can only be reached throught local variables.

### ThreadLocal

## Immutability
Having all field as final is not enough to have immutability.
Fields reference may be final but the objects it holds are not immutable.
Non-final references shouldnt be escaped!