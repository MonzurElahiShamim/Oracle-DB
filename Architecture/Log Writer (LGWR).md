---
updated_at: 2024-10-07T17:41:37.753+06:00
edited_seconds: 160
---
# Log Writer (LGWR) - Oracle Database

#### Purpose
 The Log Writer (LGWR) is a critical background process responsible for writing the contents of the **Redo Log Buffer** to the **redo log files** on disk.
#### Function:
- LGWR writes redo entries (which record changes made to data) from memory (Redo Log Buffer) to the **online redo log files**.
- It ensures that committed transactions are saved and helps in recovering the database in case of failure.
#### When it Writes:
- On **commit** of a transaction.
- When the **Redo Log Buffer** is one-third full.
- Before the **Database Writer (DBWn)** writes dirty buffers to disk.
- When a **checkpoint** occurs.
#### Importance
- ! LGWR ensures data *durability* and *recoverability* by maintaining a record of all changes in the redo log files, which can be used for recovery after crashes.
