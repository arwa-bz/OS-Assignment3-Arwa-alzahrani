# Assignment 3 - Complete Documentation

**Student Name**: [Arwa_Bakheet_Alzahrani]  
**Student ID**: [445052160]  
**Date Submitted**: [6/5/2026]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [https://drive.google.com/file/d/1jb4R6cRMYVzjPv5_OS0s5c_DXjniDcKh/view?usp=drivesdk]

**Video filename**: `445052160_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [4/5/2026, 8:00 pm]
**What I implemented**: 
I analyzed the given code and found shared resources that needed to be synchronized, like counters and the execution log.
**Challenges encountered**: 
recognizing the locations of race situations in multithreaded execution.
**How I solved it**: 
tracked shared variable access and went over concurrency principles from the textbook.
**Testing approach**: 
ran the program several times to see varying outcomes.
**Time spent**: 
2 hours
---

### Entry 2 - [4/5/2026, 10:00 pm]
**What I implemented**: 
ReentrantLock was added to safeguard the execution log and shared counters.
**Challenges encountered**: 
Ensuring all critical sections are properly protected.
**How I solved it**: 
Use try-finally to wrap all shared operations in lock.lock() and lock.unlock().
**Testing approach**: 
confirmed that there were no erroneous increments or corrupted logs.
**Time spent**: 
2 hours 
---

### Entry 3 - [5/5/2026, 12:00 am]
**What I implemented**: 
Added Semaphore to control CPU access.
**Challenges encountered**: 
Knowing where to add acquire() and release().
**How I solved it**: 
Placed acquire() at start of run() and release() in finally block.
**Testing approach**: 
make sure that only one process is active at a time.
**Time spent**: 
2 hours
---

### Entry 4 - [5/5/2026, 2:00 am]
**What I implemented**: 
Integrated synchronization in runToCompletion().
**Challenges encountered**: 
Avoiding deadlock.
**How I solved it**: 
used try-finally to ensure semaphore release.
**Testing approach**: 
verified that the program finished without hanging.
**Time spent**: 
2 hours
---

### Entry 5 - [5/5/2026, 4:00]
**What I implemented**: 
tested the entire system and confirmed the accuracy of the output.
**Challenges encountered**: 
Ensuring consistency across multiple runs.
**How I solved it**: 
verified statistics and repeated testing.
**Testing approach**: 
Ran program 5+ times.
**Time spent**: 
2 hours
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[The original code contains two race situations.
First, a shared counter that is increased by several threads is called contextSwitchCount. Multiple threads could read and update the value at the same time without synchronization, resulting in lost updates. Inaccurate context switch counts result from this.
Second, an ArrayList, which is not thread-safe, is used by the executionLog. Concurrent changes may cause runtime problems or incorrect data. The internal structure of the list may become corrupted if multiple threads add elements at the same time.]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[Only one thread can access a crucial part at a time thanks to ReentrantLock's mutual exclusion feature. Counters and the execution log are examples of shared variables that are protected by it.
Access to a restricted set of resources is managed by a semaphore. This method ensures that only one process runs at a time by simulating CPU access with a semaphore with a single permit.
Semaphores are used to manage resource access, whereas locks are intended to secure data.]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[When several threads wait endlessly for resources owned by one another, a deadlock occurs.

There are two methods of prevention:
1.Using a consistent locking order
2.Releasing resources using try-finally

By making sure that locks and semaphores are always released in a finally block, deadlock is avoided in this approach. This ensures that, even in the event of an exception, resources are not retained indefinitely.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[I secured all shared counters with a single lock (coarse-grained locking). This guarantees accuracy and streamlines implementation.

Simplicity and a lower chance of deadlocks are two benefits of coarse-grained locking. However, because threads must wait even when they are accessing distinct variables, it decreases concurrency.

By safeguarding each variable independently, fine-grained locking permits greater concurrency, but it also raises complexity and error risk.

Better concurrency would result from fine-grained locking because the counters are independent. But I went with coarse-grained locking for ease of use and dependability.]
---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, totalWaitingTime
**Why they need protection**: 
They are shared among multiple threads and updated concurrently.
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```
java
lock.lock();
try {
    contextSwitchCount++;
} finally {
    lock.unlock();
}
```

**Justification**: 
Ensures atomic updates and prevents race conditions.
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog (ArrayList)
**Why it needs protection**: 
ArrayList is not thread-safe and concurrent access may cause corruption.
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```
java
lock.lock();
try {
    executionLog.add(message);
} finally {
    lock.unlock();
}
```

**Justification**: 
Ensures safe concurrent modifications.
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
Control CPU access
**Number of permits and why**: 
1 permit to simulate single CPU
**Where implemented**: 
Inside run() and runToCompletion()
**Code snippet**:
```
java
SharedResources.cpuSemaphore.acquire();
try {
    // execution
} finally {
    SharedResources.cpuSemaphore.release();
}
```

**Effect on program behavior**: 
simulates actual CPU scheduling by ensuring that only one task runs at a time.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```
bash
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
```

**Results**: 
(Consistent statistics across runs.)
??? Synchronization Statistics ???
Total Context Switches: 36
Total Completed Processes: 18
Total Waiting Time: 1395182ms
Average Waiting Time: 77510ms

??? Process Summary Table ???
Process    Priority     Burst Time   Waiting Time
????????????????????????????????????????????????
P1         3            7980         65922       
P2         1            4804         69932       
P3         3            6565         70746       
P4         2            8548         98899       
P5         5            3625         16241       
P6         4            7496         77367       
P7         5            5166         80873       
P8         1            2621         27959       
P9         4            8150         99467       
P10        4            5699         86085       
P11        1            4373         87793       
P12        3            2891         42742       
P13        3            6291         88173       
P14        3            5406         90473       
P15        2            9193         99625       
P16        3            5634         95920       
P17        3            5836         97562       
P18        2            7489         99403       

??? Execution Log Summary ???
Total log entries: 72
**Why synchronization is necessary**: 
(Due to race circumstances, shared counters may generate inaccurate values in the absence of synchronization.)

**Conclusion**: 
Synchronization ensures correctness and consistency.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 
Observed execution log during concurrent access.
**Results**: 
No ConcurrentModificationException occurred.
**What this proves**: 
Execution log is properly synchronized.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
Completed processes = total processes

**Actual values**: 
Total Context Switches: 36
Total Completed Processes: 18
Total Waiting Time: 1395182ms
Average Waiting Time: 77510ms

**Analysis**: 
All processes completed successfully and statistics are correct.
---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]
Different random process counts and burst times
**Purpose**: 
Test robustness
**Results**: 
Program handled all cases correctly
**What I learned**: 
Synchronization works regardless of workload size
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[For multithreaded systems to guarantee data consistency and proper program behavior, synchronization is crucial. I discovered that when several threads use shared resources without appropriate control, race conditions arise, producing unpredictable outcomes. To ensure that only one thread may access a crucial portion at a time, mutual exclusion can be enforced by using techniques like ReentrantLock. Additionally, I discovered that semaphores can be used to simulate CPU access in this assignment and regulate access to restricted resources. Finding every crucial portion and making sure it was adequately safeguarded was one of the main challenges. Another crucial realization is that, in order to prevent problems like deadlock, synchronization must always be applied carefully. In general, synchronization increases reliability, although performance factors must be taken into account.]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
banking systems that allow several people to access the same account. By avoiding deposits and withdrawals from interfering with one another, synchronization helps to avoid inaccurate balances.

**Example 2**: 
CPU scheduling in operating systems, in which several processes vie for CPU time. Synchronization guarantees equitable and regulated CPU access.
---

### How I would explain synchronization to others:

[Managing access to a shared resource is analogous to synchronization. Consider a large number of individuals using a single bathroom. In the absence of regulations, people may enter simultaneously, leading to disputes. Similar to a key, a lock only permits one person to enter at a time. Similar to having a set number of keys, a semaphore permits a certain number of users to access the resource at once. Synchronization in programming makes guarantee that different threads can access shared data without interfering with one another.]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/arwa-bz/OS-Assignment3-Arwa-alzahrani.git

**Number of commits**: 11 commits

**Commit messages**: 
1. change my student id to 445052160
2. add ReentrantLock and semaphore for synchronization
3. add semaphore and its package for synchronization
4. protect shared variable name (contextSwitchCount) with lock and finally release the lock
5. protect shared variable name (completedProcessCount) with lock and finally release the lock
6. protect shared variable name (totalWaitingTime) with lock and finally release the lock
7. protect shared variable name (executionLog) with lock and finally release the lock
8. add cpu semaphore control in process execution then releasebit in finally block
9. apply semaphore in runToCompletion()
10. answering the quastions 
11. Added video demonstration link

---

## Summary

**Total time spent on assignment**: 15 hours

**Key takeaways**: 
1. Synchronization is necessary to prevent race conditions in multithreaded programs.
2. Locks ensure mutual exclusion, while semaphores control access to resources.
3. Proper synchronization improves correctness but requires careful design.

**Most challenging aspect**: 
identifying every crucial area and making sure it was appropriately secured without causing deadlocks.
**What I'm most proud of**: 
Successfully implementing both locks and semaphores and achieving consistent and correct results across multiple runs.
---

**End of Documentation**
