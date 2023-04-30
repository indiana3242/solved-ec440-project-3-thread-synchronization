Download Link: https://assignmentchef.com/product/solved-ec440-project-3-thread-synchronization
<br>



<h1>Project Goals</h1>

<ul>

 <li>To enable numerous threads to safely interact</li>

 <li>To extend our threading library with basic synchronization primitives</li>

</ul>

<h1>Collaboration policy</h1>

<ul>

 <li>You are encouraged to discuss this project with your classmates/instructors but are required to turn in your own solution.</li>

 <li>You must be able to fully explain your solution during oral examination.</li>

</ul>

Deadline

It is due on Monday, November 4<sup>th</sup> 16:30 ET (no deadline extensions or late submissions).

<h1>Project Description</h1>

In Project 2 we implemented a basic threading library that enables a developer to create and perform computations in multiple threads. In this Project we are going to extend the library to facilitate communication between threads.

To achieve this, we will need to:

<ol>

 <li>Implement a basic locking mechanism that switches the interactivity of a thread on/off. At certain stages of the execution of a thread, it may be desirable to prevent the thread from being interrupted (for example, when it manipulates global data structures such as the list of running threads). For this, you will need to create two functions: <strong><em>void lock()</em></strong> and <strong><em>void unlock()</em></strong>. Whenever a thread calls <strong><em>lock</em></strong>, it can no longer be interrupted by any other thread. Once it calls <strong><em>unlock</em></strong>, the thread resumes its normal status and will be scheduled whenever an alarm signal is received. Note that a thread is supposed to call <strong><em>unlock</em></strong> only when it has previously performed a <strong><em>lock</em></strong> Moreover, the result of calling <strong><em>lock</em></strong> twice without invoking an intermediate <strong><em>unlock</em></strong> is undefined.</li>

 <li>Implement pthread_join POSIX thread function:</li>

</ol>

<strong><em>int pthread_join(pthread_t thread, void **value_ptr);</em></strong>

This function will postpone the execution of the thread that initiated the call until the target thread terminates, unless the target thread has already terminated. There is now the need to correctly handle the exit code of threads that terminate. On return from a successful <strong><em>pthread_join</em></strong> call with a non-NULL value_ptr argument, the value passed to <strong><em>pthread_exit</em></strong> by the terminating thread shall be made available in the location referenced by value_ptr

The results of multiple simultaneous calls to <strong><em>pthread_join</em></strong> specifying the same target

thread are undefined.

<ol start="3">

 <li>Add semaphore support to your library. As discussed in class, semaphores can be used to coordinate interactions between multiple threads. You have to implement the following functions (for which you should include <em>h</em>):</li>

</ol>

<h2>a. int sem_init(sem_t *sem, int pshared, unsigned value);</h2>

This function will initialize an unnamed semaphore referred to by <em>sem</em>. The <em>pshared</em> argument always equals to 0, which means that the semaphore pointed to by <em>sem</em> is shared between threads of the process. Attempting to initialize an already initialized semaphore results in undefined behavior

<ol>

 <li><strong><em>int sem_wait(sem_t *sem);</em></strong></li>

</ol>

<em>sem_wait</em> decrements (locks) the semaphore pointed to by <em>sem</em>. If the semaphore’s value is greater than zero, then the decrement proceeds, and the function returns immediately. If the semaphore currently has the value zero, then the call blocks until it becomes possible to perform the decrement (i.e., the semaphore value rises above zero). For this Project, the value of the semaphore <strong>never</strong> falls below zero.

<ol>

 <li><strong><em>int sem_post(sem_t *sem);</em></strong></li>

</ol>

<strong><em>sem_post</em></strong> increments (unlocks) the semaphore pointed to by <em>sem</em>. If the semaphore’s value consequently becomes greater than zero, then another thread blocked in a <strong><em>sem_wait</em></strong> call will be woken up and proceeds to lock the semaphore. Note that when a thread is woken up and takes the lock as part of <em>sem_post</em>, the value of the semaphore will remain zero.

<ol>

 <li><strong><em>int sem_destroy(sem_t *sem);</em></strong></li>

</ol>

<strong><em>sem_destroy</em></strong> destroys the semaphore specified at the address pointed to by <em>sem</em> which means that only a semaphore that has been initialized by <strong><em>sem_init </em></strong>should be destroyed using <strong><em>sem_destroy</em></strong>. Destroying a semaphore that other threads are currently blocked on (in <strong><em>sem_wait)</em></strong> produces undefined behavior. Using a semaphore that has been destroyed produces undefined results, until the semaphore has been reinitialized using <strong><em>sem_init</em></strong>.

Details/ Implementation Hints:

<ol>

 <li>In Project 3 we extend the functionality of the threading library you built in the previous project. If you implement <strong><em>pthread_join</em></strong>, <strong><em>lock</em></strong> and <strong><em>unlock</em></strong> before Monday October 28<sup>th</sup> 16:30ET, you will get 5% extra credit for the assignment.</li>

 <li>Adding the <strong><em>lock</em></strong> and the <strong><em>unlock</em></strong> functions is straightforward. To lock, you can make use of the <strong><em>sigprocmask</em></strong> function to ensure that the current thread can no longer be interrupted by an alarm signal. To unlock, simply re-enable (unblock) the alarm signal, again using <strong><em>sigprocmask</em></strong>. Use <strong><em>lock</em></strong> and <strong><em>unlock</em></strong> functions to protect all accesses to global data structures.</li>

 <li>When implementing the <strong><em>pthread_join</em></strong> function, you will want to introduce a BLOCKED status for your threads. If a thread is blocked, it cannot be selected by the scheduler. A thread becomes blocked when it attempts to join a thread that is still running.</li>

 <li>In addition to blocking threads that wait for (attempt to join) active threads, you might also need to modify <strong><em>pthread_exit</em></strong>. More specifically, when a thread exits, its context cannot be simply cleaned up. You will need to need to retain its return value since other threads may want to get this return value by calling <strong><em>pthread_join</em></strong> Once a</li>

</ol>

thread’s exit value is collected via a call to <strong><em>pthread_join</em></strong>, you can free all resources related to this thread (analogous to zombie processes).

<ol start="5">

 <li>One question that may arise is how you can obtain the return (exit) value of a thread that does not call <strong><em>pthread_exit</em></strong> We know that, in this case, we have to use the return value of the thread’s start function (run <strong><em>man</em></strong> <strong><em>pthread_exit</em></strong>). A function normally passes its return value back to the caller (the calling function) via the <em>$rax</em> register. To access this value, some assembler code is needed. In the following, we assume that you have set up the stack of every new thread so that it returns to the function <strong><em>pthread_exit_wrapper</em></strong>. <strong>Note:</strong> This is different from returning directly into <strong><em>pthread_exit</em></strong>. You can then use the following code for this wrapper function: <strong><em>void pthread_exit_wrapper()</em></strong></li>

</ol>

<strong><em>      {</em></strong>

<strong><em>          unsigned long int res;           asm(“movq %%rax, %0
”:”=r”(res));           pthread_exit((void *) res);</em></strong>

<strong><em>      }</em></strong>




<ol start="6">

 <li>You can see that this wrapper function uses inline assemby to load the return value of the thread’s start function (from register <em>eax</em>) into the <em>res</em> Then, it performs the desired call to <strong><em>pthread_exit</em></strong>, using the return value of the thread’s start function as the argument.</li>

 <li>To implement semaphores include <strong><em>semaphore</em></strong><em>.h</em> that can be found in <em>/usr/include/bits.</em> This header contains the definition of the type <em>sem_t</em>. You will notice that this struct/union is likely not sufficient to store all relevant information for your semaphores. Thus, you might want to create your own semaphore structure that stores the current value, a pointer to a queue for threats that are waiting, and a flag that indicates whether the semaphore is initialized. Then, you can use one of the fields of the <em>sem_t</em> struct (for example, <em>__align</em>) to hold a reference to your own semaphore.</li>

 <li>Once you have your semaphore data structure, start implementing the semaphore functions as described above. Make sure that you test a scenario where a semaphore is initialized with a value of 1, and multiple threads use this semaphore to manage access to a critical code section that requires mutual exclusion (i.e., they invoke <strong><em>sem_wait</em></strong> before entering the critical section). When using your semaphore, only a single thread should be in the critical section at any time. The other threads need to wait until the first thread exits and calls <strong><em>sem_post</em></strong>. At this point, the next thread can proceed.</li>

</ol>