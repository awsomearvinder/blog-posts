# Async/Await and Threading
This blog post is just going to be a rant of me taking about async await, not any language in particular, however it will be focused more on languages like javascript and C#, then my usual rust. Too start things off, I should probably explain what these two concepts are for, where they are similar, and where they differ.

Async/Await and Threading are both ways of achieving concurrency. They both allow code to run at the same time, however the two go through this in a different way. To start with, threads are pieces of code that run on a physical thread on your machine. Your machine has an X number of threads, and your OS will run your code on those threads according to how you've threaded it.

A well written multithreaded program that is bound by cpu power will scale well with threads, because they can utilize more cores and physical threads on your CPU. In order to explain this, to start off with we need to understand how threads work on a hardware/OS level.

Your CPU isn't a singular device that computes information, but rather multiple devices that cna all compute information simultaneously. For example, a modern i7 will have 8 cores, and 16 physical threads. At any given time, you can do 16 computations at once. Because these are seperate on a hardware level, properly threading a single threaded program to use 16 threads on a system with 16 physical threads could result in a optimal 16x CPU power increase!

In order to thread your application, you have to split your code up into functions that can run at the same time, this ofcourse can be bug heavy if multiple threads can access the same information and state, and comes with it's own complications, (such as dataraces, deadlocks, etc.) not to mention that creating threads constantly is *not* performant. It takes time to create a new thread, and creating more threads then the machine has can cause something called context switching, where the OS has to switch between different threads because the machine has less physical threads then software threads.

Things that don't use a lot of cpu time, don't utilize a thread 100% of the time, and therefore don't require much more computation then the relatively cheap context switch switching to that thread. At a large scale though, where you have thousands of threads in your application, even if the context switches are just switching to check in on something that dosen't require much cpu power (e.g. reading from a disk) it can cause slow downs. Thread pools can alleviate this to some extent, only creating a certain number of threads, and passing functions to it, but that leaves it's own issues. 

In order to understand the issues with threadpools, you have to understand what threadpools are. Threadpools small "pools" of threads that you can pass work to. The threads don't get created or destroyed. You as a programmer simply keep passing functions to them, and they don't suffer the issue from the overhead of tons of context switching since only a small amount of threads exist at any given time. For compute workloads, this is once again ideal. However, they suffer from a issue with latency. In a threadpool, if all the threads are being utilized, new functions passed in won't run until the previous ones finish running.

This is *extremely* important for a webserver. If a server has 8 threads, and each thread represents a client, using a 8 thread workpool would only allow the web server to ever simultaneously handle 8 clients at any given time! Because the majority of the time your just waiting for a response (could be anywhere from milliseconds, to seconds) this could cause a giant build up of clients that aren't getting any cpu time on the server!

This problem, of how to handle a large number of connections at once, was dubbed the C10k problem (how do I handle 10,000 concurrent connections). You clearly need to run multiple pieces of code at once to handle multiple clients, but you can't create a new thread per each client, that's an expensive operation due to all the context switching at a large number of these threads. You can't use a threadpool, because a buildup could happen due to the response times of the clients.

This is where Async/Await and the idea of Futures shine. In the webserver case, note that the issue is that the majority of the time, your waiting on the client to respond. You aren't actually *actively* doing anything with the client, no work is being done. The thread is simply sitting there silently until the OS decides to wake it up via a context switch to see if its ready to keep doing work, if not, it'll switch to the next thread. This is *extremely* wasteful, because most of the time nothing is happening on these threads.

Futures (or Promises) are the idea of representing a value that isn't actually there, only something that hopefully "will" be there sometime in the future. It's a "Promise" that eventually, the value will come back. With this, you can do code concurrently without threads because the majority of the time those threads on the web server were simply waiting on an external resource, not actually doing work. 

As a code example, a future could be something like this, (note, I am writing this in C# syntax, however I haven't tried to compile it at all)

```CSharp
var a = client1.getClientResponse(); //This function returns a value that will eventually be stored in a.
var b = client2.getClientResponse(); //This function returns a value that will eventually be stored in b.
await a; //This says to wait for when the value b has been returned.
await b; //This says to wait for when the value b has been returned.
```
In this example, client1 and client2 get their response at the same time. The code only stops when it reaches a point where there's an "await" which basically tells the compiler to wait for the value to actually arrive. 

Because this code isn't principally threaded, only one piece of code can actively run at any given time, so what gives? How can we be running getClientResponse() on client1, and client2 at the same time?

The key is the await points, any place where you await something, for a certain amount of time, nothing is being ran on the CPU. At this point in time, it runs any other asynchronous work that needs to be done. In other words, somewhere in getClientResponse(), there's an await, and while the code is waiting for the value, if there's any other code that needs to run, it runs in the background. 

In the code we have up above, if this code was in another function, whenever it reached an await(either await a, or await b), C# would check if it had any other work to do in the background, and if it did, it would run that rather then waiting for the value, when it's done running that (or reached an await) then it goes back and checks if the value has returned. If it has, it continues running the function, if it hasn't, it runs other code it needs to in the background. 

In this way, we can achieve concurrency in a single thread! The pitfalls of this approach being, you lose the benefits on having more physical units to do computations. Cores are physical parts of your CPU that can do work simultaneously, for a web server, generally, this dosen't matter, however async await is likely not the best choice for making something that's computationally expensive. Other then that, async/await is groovy!

As a summary:
Threads allow concurrency, and increase your CPU power you have at your disposal, however at a large scale context switches can slow them down.

ThreadPools also increase your CPU power, however, there's no guarentees that all your code will run at the same time - it saves the benefit of having a bunch of context switches, however it has to wait for the previous code to finish running on its threads before going to the next bit of code.

Async/Await do not increase your CPU power, however, it dosen't suffer from context switches nor does it suffer from the latency issues of threadpools. 

Each of these things have a time and a place, knowing which to use is an important piece to add to your toolkit as a software developer. 