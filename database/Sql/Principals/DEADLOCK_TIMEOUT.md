


## How the Database Handles Deadlocks

### 1. Deadlock Detection (The "Janitor")

Most modern databases (PostgreSQL, MySQL, SQL Server) run a **Deadlock Detector** background process.

- **The Logic:** It builds a "Wait-for Graph." If it sees a cycle ($A \to B \to A$), it realizes they will wait forever.
    
- **The Action:** The DB **kills one of the transactions** (the "Victim") and rolls it back to break the loop. The other transaction is then allowed to finish.
    
- **Interview Tip:** Mention `deadlock_timeout` (Postgres default is 1s). The DB doesn't check for deadlocks instantly because checking is CPU-intensive; it waits for this timer to expire before searching for cycles.
    

### 2. Row-Level vs. Table-Level Locking

Deadlocks often happen because a query accidentally locks more than it needs.

- **The Problem:** If you don't have an index on a `WHERE` clause, the DB might perform a **Table Lock** (locking every row) just to find one record.
    
- **The Fix:** Proper **Indexing** ensures the DB only places **Row-Level Locks**, drastically reducing the "surface area" for two transactions to collide.
    

### 3. Intent Locks (The "Warning Sign")

Before a DB locks a specific row, it places an **Intent Lock** on the table level.

- **The Logic:** It’s like putting a sign on a building's front door saying, "Someone is working in a room upstairs."
    
- **The Benefit:** It prevents another transaction from trying to lock the _entire table_ (e.g., for a schema change) while you are mid-update on a single row.
    

---

## 🛠️ DB-Side Prevention Strategies

| **Strategy**     | **DB Command/Setting**              | **What it does**                                                                                                               |     |
| ---------------- | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ | --- |
| **Lock Timeout** | `SET lock_timeout = '2s'`           | Prevents a query from waiting forever for a lock.                                                                              |     |
| **Skip Locked**  | `SELECT ... FOR UPDATE SKIP LOCKED` | **(Crucial for Queues)** Tells the DB: "If someone else is working on this row, just ignore it and give me the next free one." |     |
| **No Wait**      | `SELECT ... FOR UPDATE NOWAIT`      | Immediately throws an error if the row is busy, instead of waiting.                                                            |     |




"The database engine uses a **Deadlock Detector** that periodically scans for 'Wait-for' cycles. Once a circular dependency is found, the DB engine chooses a **'Victim'**—usually the transaction that has done the least amount of work—rolls it back, and throws a specific error (like `40P01` in Postgres).

To minimize this, I ensure we have **strong indexing** to maintain row-level locking and use **`SKIP LOCKED`** for background workers so they don't fight over the same rows."

