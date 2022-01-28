# Thereads

Es gibt Threads innerhalb der Prozesse. Wenn Threads gestertet sind, sind sie unabhängig von einander.

A multi-threaded process has multiple, concurrent points of execution. A thread can be thought of like a separate
process, however sharing the same address space

> Definition
> A thread is an abstraction for the current point of execution (i.e. the program counter, PC/IP), allowing a process to run concurrently at different code locations. Each thread of aprocess shares the **same virtual address space, open file descriptors** and other things. However, it is running with its own PC and registers. Also, every thread has its **own stack** and may also have access to other data exclusively (sometimes called thread-local storage).

## Why Threads?

- Parallel: Distributing work on multiple CPUs for operations on large data structures which can be done in parallel (
  thanks to shared addr. space ). Bictoin-Mining
- Waiting for I/O which block a process for a long time. Using threads, a process can do other work while waiting for
  completion.
- Periodical: Work which needs to be done periodically in a program (and possibly in the background).

Thread Creation und Example: Folie 9 -

# Critical Sections

__Uncontrolled Scheduling__: Bsp:
2 Threads erhöhen den selben counter:

```assembly
1192: mov 0x2eb4(%rip),%eax # 404c <counter> 
1198: add $0x1,%eax
119b: mov %eax,0x2eab(%rip) # 404c <counter>
```

- T1 liest den Wert des Counters,
- T1 macht die Incrementation
- Timer Interrupt! Es gibt einen Context-Switch
- T2 liest den Wert des Counters
- T2 macht die Incrementation
- T2 speichert neuen Wert
- ... T1 speicher sein (veralteter) Wert

> __Dies wird RACE CONDITION genannt__: Das Resultat ist je nach dem unterschiedlich. Es ist kein deterministisches Program sondern ein indeterministisches!

> Weil dieser Code Race Condition verursachen kann, nennen wir diesen Code "__Critical Section__": is a section of co de which accesses a shared resource and may b e prone to race conditions

## Atomicity

A possible solution for race conditions could be __atomic operations__:

Supported by the HW with a more powerful instruction, incrementing a counter in memory could be reduced to a single
instruction.

In general, such instructions are not available: There are too many different typ es of critical sections and their
implementation in HW is too costly.

Instead, we will resort to synchronization primitives , making use of the HW and some support from the OS to build
powerful mechanisms for dealing with critical sections.

## Locks

A __mutual exclusion__ lock or mutex is just a variable which holds the state of the lock:

- available (also unlocked or free): no thread holds the lock
- acquired (also locked or held): exactly one thread holds the lock and (hopefully) is in a critical section

In general, larger critical sections or multiple sections using the same lock tend to reduce concurrency.

## HW Locking Primitives

- Correctness: Does the lock work correctly? Does it provide mutual exclusion?
- Fariness: Does a thread waiting for the lock get a fair chance of obtaining it? Can a thread starve?
- Performance: How much is the added overhead when using the lock? Especially, when a single thread runs the same code
  with- and without the lock?

### 1) Disabling Interrupts

- Simple and easy to understand

- This approach is not practicble. (except for some code in the OS). Multiprocessors
- The OS looses controll (no timer interrupt), malware can probably make profit out of an interrupt and "lock" the
  computer when getting a endless lock
- problems e.g. with I/O when lock is too long
- inefficient

Löst nicht, ist viel zu riskant

### 2) A Sinple Flag Variable

Use a simple flag variable to denote if a lock is available or not

spin-waiting: Frisst sehr viel Zeit von CPU.

**Why Flag Variables Fail**

-> Folie 32 It is also not fair: there is no guarantee, that a thread will obtain a lock at a certain point in the
future (depends on scheduling).

Keine Garantie, dass es funtioneiert.

### 3) Valid Spin Lock

Spin Lock ist gültig, wenn man TestAndSet braucht.

-> TestAndSet: Folie 33, 34

Spin locks, due to the atomicity of the TestAndSet instruction, are correct and provide mutual exclusion

However, they do not provide fairness: Depending on scheduling, a thread may simply spin forever and may be prone to
starvation.

_Performance_ depends on the system in use:

- For a single CPU system , performance impacts may be servere
    - Assume the thread holding the lock is preempted
    - In the worst case, all other N-1 threasds are scheduled first and each of them spins during its entire time slice.
      . .
- On systems with multiple CPUs, performance is reasonable (if no. threads ≈ no. of CPUs)
    - If assuming, that threads are scheduled on different CPUs and critical sections are short

#### Compare And Swap

Ein bisschen dasselbe wie oben.

#### Load-Linked, Store-Conditional

Abhängig von der Architecture, aber ungefähr dieselbe Funktionsweise.

### 4) Ticket Lock

__yield__: is a simply system call that moves the caller from the runnig state to the ready state. Another Thread can go
to running. The yielding thread essentially deschedules itself.

Unlock icr den turn, ticket wird inc mit FetchAndAdd wenn neu (Dies ist atomic)

Es ist nicht möglich, dass zwei denselben Turn haben, es ist auch Fair: Der erste der gekommen ist, ist auch am ersten
drann.

Regarding performance however, things have not changed. Ticket locks are a form of spin locks and thus have the same
issues.

--> Seite mit Code nochmals anschauen

## Comparing Locks

| Lock Type         | Correctness | Fairness | Performance |
| ----------------- | ----------- | -------- | ----------- |
| 1) Disabled  Interrupts | OK | - | - |
| 2) Flag Variables | - | - | - | - |
| 3) Spin Locks | OK | - | -|
| 4) Ticket Loks | OK | OK | - |

As can be seen, none of the locks provides a good solution for the performance issues.

# Summary:

- A __critical section__ is a piece of code that accesses a shared resource, usually a variable or data structure.
- A __race condition__ arises if multiple threads of execution enter the critical section at roughly the same time; both
  attempt to update the shared data structure, leading to a surprising (and perhaps undesirable) outcome.
- An __indeterminate program__ consists of one or more race conditions; the output of the program varies from run to
  run, depending on which threads ran when. The outcome is thus not deterministic, something we usually expect from
  computer systems.
- To avoid these problems, threads should use some kind of __mutual exclusion__ primitives; doing so guarantees that
  only a single thread ever enters a critical section, thus avoiding races, and resulting in deterministic program
  outputs

