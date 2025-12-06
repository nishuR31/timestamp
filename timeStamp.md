# Timestamp-Based Concurrency Control (DBMS)


---

## Table of Contents

* [Intro](#student-1-intro)
* [What is Timestamping](#what-is-timestamping)
* [Why Timestamping Matters](#why-timestamping-matters)
* [Basic Example](#basic-example)
* [Questions](#student-1-questions)
* [Rules](#student-2-rules)
* [Read Rule](#read-rule)
* [Write Rule](#write-rule)
* [Examples](#basic-example-2)
* [Conflict Handling](#conflict-handling)
* [Questions](#student-2-questions)
* [Strict Timestamp](#student-3-strict-timestamp)
* [Strict Read/Write](#strict-readwrite)
* [Benefits and Issues](#benefits-and-issues)
* [Questions](#student-3-questions)
* [Overall Summary](#overall-summary)
* [Practice](#practice)

---

## Intro

Concurrency control keeps multiple transactions from messing each other up when they run at the same time.

Timestamp-based concurrency control avoids locking.
Instead of blocking, it orders transactions by time.
Older transactions logically come first, even if physically running later.

This makes the system behave as if everything happened in a consistent order.

---

## What is Timestamping

Each transaction T gets a unique timestamp when it starts.
Example:

TS(T1) = 5
TS(T2) = 10

T1 is older.
This means T1’s actions should appear earlier in the final schedule.

---

## Why Timestamping Matters

* No locks
* No deadlocks
* Conflicts handled automatically
* Execution tries to follow timestamp order

Think of timestamps like a queue. Whoever arrived earlier has the right to be seen first.

---

## Basic Example

Two transactions want X.

* T1 writes X
* T2 tries writing X

Since T1 is older, the DB forces logical order:

If T2 violates ordering, T2 gets aborted and restarted later with a new timestamp.

Older wins. Younger loses. Database stays consistent.

---

## Questions

---

### Q1. Why do we avoid deadlocks here?

---

### Q1. Why do we avoid deadlocks here?

> Because there are no locks involved.

---

### Q2. What tells us who is older?

---

### Q2. What tells us who is older

> Timestamp assigned when the transaction started.

---

### MCQ: If TS(T1) < TS(T2), who has priority
A. T1
B. T2

---

### MCQ: If TS(T1) < TS(T2), who has priority
A. T1
B. T2
> Correct: A

---

## Rules

Timestamp ordering uses two timestamps per data item X:

* R_TS(X): time of last read on X
* W_TS(X): time of last write on X

These help the DB decide if a new read/write is safe or should be aborted.

---

## Read Rule

Transaction T wants R(X):

If W_TS(X) > TS(T) then abort T
Else read and update R_TS(X)

Meaning:
If a younger transaction already wrote X, older T reading now would read a value from the “future”. That breaks logical order.

So T aborts and restarts.

---

## Write Rule

Transaction T wants W(X):

If R_TS(X) > TS(T) OR W_TS(X) > TS(T) then abort T
Else write and update W_TS(X)

Translation:
If a younger transaction already touched X in a way that breaks order, the write is unsafe. Abort.

---

## Basic Example 2

Let:
TS(T1)=5
TS(T2)=10

Suppose T2 writes X first, so W_TS(X)=10.

Now T1 tries R(X).
But W_TS(X) = 10 > TS(T1) = 5.

T1 would be reading a value that belongs to a younger transaction. That’s reading from the future. Abort T1.

---

## Conflict Handling

When conflict happens:

* Younger transaction usually loses
* Because older has logical priority

This bias ensures serializable order, even if real timing is different.

---

## Questions

--- 

### Q1. Why do we abort T when reading future values

---

### Q1. Why do we abort T when reading future values 
> To keep logical order of older first, younger second.

--- 

### MCQ: If a younger transaction already wrote X, an older transaction trying to read should
A. Read anyway
B. Abort
C. Wait
D. Ignore X

---

### MCQ: If a younger transaction already wrote X, an older transaction trying to read should
A. Read anyway
B. Abort
C. Wait
D. Ignore X
> Correct: B

### MCQ: Which timestamp tracks last write
A. R_TS
B. W_TS

---

### MCQ: Which timestamp tracks last write
A. R_TS
B. W_TS
> Correct: B

---

## Strict Timestamp

Basic timestamping aborts aggressively.
This can cause cascading aborts (one abort triggers others).

Strict timestamp ordering is a variation that tries to prevent this.
Instead of aborting instantly, it delays operations until they are safe to execute.

---

## Strict Read/Write

Strict Read:
T can read X only if:

* W_TS(X) <= TS(T)
* The transaction that wrote X has committed

Strict Write:
T can write X only if:

* R_TS(X) <= TS(T)
* W_TS(X) <= TS(T)
* All previous readers/writers have committed

If not safe, T waits instead of immediate abort.

---

## Benefits and Issues

Benefits:

* Conflict serializable
* No locks, so no deadlocks
* Prevents cascading aborts (strict version)

Issues:

* Newer transactions may starve
* Timestamp checks add overhead

---

## Overall Summary

Timestamp-based concurrency control decides who acts first by using timestamps, not locks.

* Older transactions have priority
* Basic TO aborts unsafe actions immediately
* Strict TO waits to avoid cascading aborts
* Uses R_TS and W_TS to check safety
* No deadlocks because there are no locks

This makes concurrency predictable and logically ordered, while reducing typical lock-based issues.

---

## Practice

---


1. What is the use of timestamps in concurrency control
2. What is meant by reading a future value

---

### MCQ: Timestamp ordering is mainly for
A. Locking
B. Enforcing logical order
C. Allowing dirty reads
D. Random execution

---

### MCQ: Timestamp ordering is mainly for
A. Locking
B. Enforcing logical order
C. Allowing dirty reads
D. Random execution
> B


---

### MCQ: Cascading aborts happen because
A. Too many locks
B. Writes without commit confirmation
C. Old values are overwritten instantly
D. Timestamps are wrong

---

### MCQ: Cascading aborts happen because
A. Too many locks
B. Writes without commit confirmation
C. Old values are overwritten instantly
D. Timestamps are wrong
> Correct: B

---

## Thank you for listening.
### Aborting
