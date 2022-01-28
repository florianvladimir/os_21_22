# Threads Guideline

- __Keep it simple__. Above all else, any code to lock or signal between threads should be as simple as possible. Tricky thread interactions lead to bugs.
- __Minimize thread interactions__. Try to keep the number of ways in which threads interact to a minimum. Each interaction should be carefully thought out and constructed with tried and true approaches (many of which we will learn about in the coming chapters).
- __Initialize locks and condition variables__. Failure to do so will lead to code that sometimes works and sometimes fails in very strange ways.
- __Check your return codes__. Of course, in any C and UNIX programming you do, you should be checking each and every return code, and it’s true here as well. Failure to do so will lead to bizarre and hard to understand behavior, making you likely to (a) scream, (b) pull some of your hair out, or (c) both.
- __Be careful with how you pass arguments to, and return values from, threads__. In particular, any time you are passing a reference to a variable allocated on the stack, you are probably doing something wrong.
- __Each thread has its own stack.__ As related to the point above, please remember that each thread has its own stack. Thus, if you have a locally-allocated variable inside of some function a thread is executing, it is essentially private to that thread; no other thread can (easily) access it. To share data between threads, the values must be in the heap or otherwise some locale that is globally accessible.
- __Always use condition variables to signal between threads.__ While it is often tempting to use a simple flag, don’t do it.
