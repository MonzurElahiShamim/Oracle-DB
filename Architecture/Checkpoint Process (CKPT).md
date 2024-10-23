---
updated_at: 2024-10-07T21:13:24.325+06:00
edited_seconds: 190
---
# Checkpoint Process (CKPT) - Oracle Database

#### What is a Checkpoint?
- A **checkpoint** is an event where **dirty buffers** (data blocks modified in memory) are written from the **Buffer Cache** to the **data files**.
- It synchronizes the database’s memory and disk, ensuring data consistency and reducing recovery time after a failure.

## Checkpoint Process (CKPT)
#### Purpose:
- The **Checkpoint Process (CKPT)** manages the checkpoint event in the Oracle database.
- Its primary task is to update the **control file** and **data file headers** with the latest checkpoint information, signaling that the data in memory has been written to disk.

#### Key Functions:
- **Coordinate Data Writing**: CKPT signals the **Database Writer (DBWn)** process to write dirty buffers from the **Buffer Cache** to disk.
- **Update Control Files**: CKPT updates the **control files** with checkpoint details, including the latest **System Change Number (SCN)**.
- **Update Data File Headers**: CKPT writes the checkpoint information into the **data file headers**, indicating that data blocks have been written up to a certain SCN.

#### When a Checkpoint Occurs:
- **Log Switch**: When the current **online redo log** file is full, Oracle initiates a checkpoint to ensure changes in the redo log are written to disk.
- **Manual Checkpoints**: A checkpoint can be manually triggered using the `ALTER SYSTEM CHECKPOINT` command.
- **Database Shutdown**: During a **consistent shutdown**, Oracle ensures all dirty buffers are written to disk before closing the database.
- **Regular Intervals**: Oracle automatically initiates checkpoints at regular intervals, depending on configured parameters.

#### Importance of the Checkpoint Process:
- **Faster Recovery**: Checkpoints reduce recovery time by ensuring that changes made to the database before the checkpoint are safely stored on disk. This minimizes the amount of redo that must be applied during instance recovery.
- **Ensures Data Integrity**: Synchronizing memory with disk prevents data inconsistency, ensuring a stable database state.
- **Prevents Redo Log Overwrite**: During log switches, checkpoints ensure that changes recorded in redo logs are written to disk before the log is reused, preventing data loss.

## Summary:

The **CKPT Process** plays a vital role in maintaining the consistency of the Oracle database by coordinating the writing of modified data from memory to disk, updating key files with the latest SCN, and reducing recovery time after failures.
