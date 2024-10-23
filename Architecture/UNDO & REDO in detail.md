---
updated_at: 2024-09-18T11:37:25.209+06:00
edited_seconds: 170
---
# UNDO & REDO in Oracle Database

> [!abstract]+ 
> ### **UNDO**:
> - **Purpose**: Used to **reverse changes** made by uncommitted transactions and maintain **read consistency**.
> - **Key Uses**:
>   - **Transaction Rollback**: Reverts data to its pre-transaction state.
>   - **Read Consistency**: Provides a consistent view of data to other users during transactions.
>   - **Stored In**: **Undo Segments** within the **Undo Tablespace**.
>   
> ### **REDO**:
> - **Purpose**: Ensures all changes from committed transactions can be **reapplied** during recovery after a crash.
> - **Key Uses**:
>   - **Crash Recovery**: Reapplies committed changes after a system failure.
>   - **Durability**: Ensures no committed data is lost.
>   - **Stored In**: **Redo Log Files**, written by the **Log Writer (LGWR)** process.
> 
> ### **Comparison**:
> 
| **Feature**           | **UNDO**                                      | **REDO**                                      |
|-----------------------|-----------------------------------------------|-----------------------------------------------|
| **Purpose**           | Reverse uncommitted changes                   | Reapply committed changes                     |
| **Used for**          | Transaction rollback, read consistency        | Crash recovery, durability                    |
| **Where it's stored** | Undo Segments in Undo Tablespace              | Redo Log Files on disk                        |
| **When used**         | When a transaction is rolled back or for read consistency | During crash or media recovery                |
| **Responsible Process** | Server Process (SP) writes undo information | Log Writer (LGWR) writes redo logs            |
> 
> Both are essential for Oracle’s robust transaction management and data integrity.

[[Drawing UNDO and REDO.excalidraw]]
# UNDO and REDO in detail

In Oracle databases, **UNDO** and **REDO** are key components of transaction management and data recovery. They work together to ensure data consistency, integrity, and durability, even in the face of system failures or crashes.

---

### **1. UNDO**
**UNDO** is responsible for managing and storing information needed to roll back (undo) changes made by a transaction. It is crucial for **transaction control** and **read consistency** in Oracle.

#### **Purpose of UNDO**:
- **Transaction Rollback**: When a transaction is rolled back (either manually or due to a failure), Oracle uses UNDO information to reverse the changes and restore the original data state.
- **Read Consistency**: During ongoing transactions, Oracle provides consistent views of data to other users. UNDO allows them to see data as it was before any uncommitted changes were made.
- **Savepoint Support**: Allows partial rollbacks of transactions up to a defined point (called a savepoint), without affecting the entire transaction.

#### **How UNDO Works**:
- Oracle maintains **UNDO Segments** in a special tablespace (UNDO tablespace) to store changes made by transactions. Every transaction generates UNDO records that capture the "before" image of the modified data.
- If a transaction is committed, the UNDO records are eventually discarded once they are no longer needed.
- In case of a rollback, Oracle retrieves the relevant UNDO information to revert the changes made by the transaction.

#### **Key Scenarios for UNDO**:
1. **Transaction Rollback**: If a transaction is aborted or rolled back, Oracle uses UNDO to revert any changes made during that transaction.
2. **Crash Recovery**: After a system crash, any uncommitted transactions must be rolled back. UNDO ensures that the database is restored to a consistent state.
3. **Read Consistency**: While one user is modifying data, other users can still query the data as it existed before the changes, thanks to the UNDO.

#### **Components of UNDO**:
- **Undo Segments**: Store the before-image of changed data for each transaction.
- **Undo Tablespace**: A dedicated tablespace where undo segments reside.

---

### **2. REDO**
**REDO** ensures that all changes made by committed transactions can be reapplied in case of a system failure. REDO is used during crash recovery to bring the database back to a consistent state by reapplying committed transactions that may not have been written to datafiles.

#### **Purpose of REDO**:
- **Crash Recovery**: In the event of a system crash, Oracle uses the REDO logs to reapply all changes made by committed transactions. This ensures that no committed data is lost.
- **Durability**: REDO guarantees that once a transaction is committed, its changes are durable, even if the system fails before those changes are written to disk.

#### **How REDO Works**:
- Whenever a transaction makes changes to the database (inserts, updates, deletes), Oracle writes the **redo entries** into a memory structure called the **Redo Log Buffer**.
- The **Log Writer (LGWR)** process flushes these redo entries from memory to **Redo Log Files** on disk, ensuring that even if the system crashes, the committed changes can be recovered.
- **Redo Log Files** store the redo records, which are used for crash recovery or instance recovery.

#### **Key Scenarios for REDO**:
1. **Crash Recovery**: When the database restarts after a crash, Oracle uses the REDO logs to recover all committed transactions that weren’t written to datafiles.
2. **Media Recovery**: If a datafile is lost or corrupted, Oracle can use the REDO logs to replay the changes and restore the lost data.

#### **Components of REDO**:
- **Redo Log Buffer**: A memory area where redo entries are temporarily stored before being written to disk.
- **Redo Log Files**: The physical files that store the redo records on disk.
- **Log Writer (LGWR)**: The process that writes the redo entries from the Redo Log Buffer to the Redo Log Files.

---

### **3. Comparison of UNDO and REDO**

| **Feature**             | **UNDO**                                                  | **REDO**                           |
| ----------------------- | --------------------------------------------------------- | ---------------------------------- |
| **Purpose**             | Reverse uncommitted changes                               | Reapply committed changes          |
| **Used for**            | Transaction rollback, read consistency                    | Crash recovery, durability         |
| **Where it's stored**   | Undo Segments in Undo Tablespace                          | Redo Log Files on disk             |
| **When used**           | When a transaction is rolled back or for read consistency | During crash or media recovery     |
| **Responsible Process** | Server Process (SP) writes undo information               | Log Writer (LGWR) writes redo logs |

---

### **4. How UNDO and REDO Work Together**
- **UNDO** is crucial for handling **uncommitted transactions**, ensuring that changes can be rolled back.
- **REDO** ensures that **committed transactions** are durable, even if the system crashes.
- During normal operation, Oracle generates both UNDO and REDO information for each transaction. If a system crash occurs:
  1. **REDO** logs are used to **reapply committed changes** that might not have been written to the datafiles.
  2. **UNDO** information is used to **rollback any uncommitted changes**.

For example, if a system crashes while a user is in the middle of a transaction:
- **REDO** will recover all committed changes that might not yet be written to the datafiles.
- **UNDO** will ensure that any uncommitted changes made during that transaction are rolled back to maintain data consistency.

---

### **5. Practical Examples**

#### **Example of UNDO**:
- A user updates a row in a table but decides to rollback the transaction. Oracle uses the UNDO segment to restore the row to its previous state before the update.

#### **Example of REDO**:
- A transaction is committed, but before the data is written to the datafiles, the system crashes. Upon restart, Oracle uses the REDO logs to reapply the committed transaction and ensure no data is lost.

---

### **6. Importance of UNDO and REDO**
- **Data Integrity**: Together, UNDO and REDO ensure that Oracle maintains data consistency and integrity during transactions.
- **Crash Recovery**: REDO logs are fundamental for recovering a database to a consistent state after a crash, ensuring committed transactions are not lost.
- **Read Consistency**: UNDO ensures that queries always get a consistent view of the data, even while other transactions are ongoing.
  
---

### **Conclusion**
UNDO and REDO are integral parts of Oracle’s transaction management and recovery mechanisms. **UNDO** helps manage uncommitted changes and provides read consistency, while **REDO** guarantees the durability of committed changes by allowing recovery after system failures. Together, they form the backbone of Oracle's robust transaction management and recovery system.