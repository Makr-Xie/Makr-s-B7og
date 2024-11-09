---
  title: Lock & Serializability (DB System)
  pubDate: 2024-11-8
  categories: ["笔记"]
  description: 'A note of Lock and Serializability of UIUC CS 411, using for homeworrk & exam review.'
---

- [Isolation Levels and Locking](#isolation-levels-and-locking)
- [Theory of Serializability](#theory-of-serializability)
- [Two-Phased Locking — 2PL \& S2PL](#two-phased-locking--2pl--s2pl)


## Isolation Levels and Locking

- Before a read, a **shared lock** must be acquired
- Before a write, an **exclusive lock** must be acquired

**Read Committed**

1. Requires lock before read or write
2. release **shared lock** immediately after red
3. hold **exclusive lock** to the end of the transaction

**Repeatable Read**

1. Requires lock before read or write
2. Hold **both lock** to the end of the transaction

## Theory of Serializability

**Serial Schedule:** Run transactions one at a time, in a series. (Different orders might give different results.)

**Serializable schedules:** A schedule is serializable if it is equivalent to a serial schedule.

**Conflict-Serializability:**

*Conflict Actions: RW, WR, WW.*

> **Definition:** A schedule is *conflict-serializable* if it can be transformed into a serial schedule by a series of swappings of adjacent non-conflicting actions
> 

A conflict-serializable schedule is a serializable schedule, but the converse is not true in general.

**Testing for Conflict-Serializability**

Given a schedule $S$, we can construct a directed graph $G=(V, E)$ called precedence graph.

- $V$ = all transactions in $S$
- $E$ =  $T_i \to T_j$ whenever an action of  $T_i$ precedes and conflictswith an action of $T_j$ in $S$.

> ***Theorem*** :A schedule $S$ is **conflict serializable** iff its precedence graph **contains no cycles**.
> 

## Two-Phased Locking — 2PL & S2PL

**Well-Formed, Two-Phased Transactions**

- **Well-Formed**: it acquires at least a shared lock on Q before reading Q or an exclusive lock on Q before writing Q and doesn't release the lock until the action is performed
- **Two-Phased**: it never acquires a lock after unlocking one

> ***Theorem***: If all transactions are well-formed and two-phase, then any schedule in which conflicting locks are never granted ensures serializability.
> 

**Two Phase Locking Protocol (2PL)**

- T gets (S and X) locks gradually, as needed.
- T cannot request any additional locks once it releases any locks.

**Strict Two Phase Locking Protocol (S2PL)**

- T gets (S and X) locks gradually, as needed
- T holds all locks until end of transaction (commit/abort)

**Strict 2PL guarantees serializability**

- Can prove that a Strict 2PL schedule is equivalent to the serial schedule in which each transaction runs instantaneously at the time that it commits.
- This is huge: A property of each transaction (2PL) implies a property of any set of transactions (serializability). No need to check serializability of specific schedules
- Most DBMSs use 2PL to enforce serializability

A good reference: https://zhuanlan.zhihu.com/p/539168364