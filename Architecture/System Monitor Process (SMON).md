---
updated_at: 2024-10-07T21:27:58.765+06:00
edited_seconds: 120
---
# System Monitor Process (SMON) - Simplified

#### What is SMON?  
  SMON (System Monitor) is a background process in Oracle that helps keep the database healthy and running smoothly.

#### What does SMON do?
  - **Instance Recovery**: If the database crashes, SMON helps recover it when restarted by making sure the data is correct and consistent.
  - **Clean up temporary space**: SMON removes temporary data that's no longer needed, freeing up space.
  - **Merge free space**: It combines empty spaces in the database to make storage more efficient.
  - **Shrink temporary tables**: SMON reduces the size of temporary storage if it gets too big.

#### When does SMON work?
  - When the database starts up after a crash.
  - When temporary data or space needs to be managed.

#### Why is SMON important?
  - It helps recover the database after a failure.
  - It cleans and manages storage space to keep the database running efficiently.

---
---

# System Monitor Process (SMON) - Oracle Database

#### Overview:
- The **System Monitor (SMON)** is one of the essential background processes in the Oracle Database.
- SMON is responsible for performing various recovery-related and maintenance tasks to ensure the smooth running of the database.

#### Key Responsibilities:
- **Instance Recovery**: 
  - When a database instance fails and is restarted, SMON automatically performs **instance recovery** by applying changes recorded in the **redo logs** to the data files. 
  - This ensures that the database is brought to a consistent state, by rolling forward committed transactions and rolling back uncommitted ones.
  
- **Clean Up Temporary Segments**:
  - SMON cleans up **temporary segments** (used for sorting operations) that are no longer in use, releasing space back to the database.
  
- **Coalescing Free Space**:
  - SMON coalesces free space in the **tablespaces** by merging adjacent free extents, helping in efficient space utilization and reducing fragmentation.
  
- **Shrinking Temporary Tablespaces**:
  - It helps shrink **temporary tablespaces** if necessary, reducing the size of tablespaces that store temporary data.

#### Triggering of SMON:
- SMON typically operates automatically in the background but can be triggered in certain conditions such as:
  - **Database startup** (after a crash or failure).
  - When temporary segments or space management activities need attention.

#### Importance:
- SMON ensures that the database is consistently available and recovered after crashes.
- It helps in managing and optimizing database storage, ensuring better performance and efficient space utilization.
  
#### Summary:
- The **SMON process** is critical for **instance recovery**, **cleanup of temporary segments**, **free space coalescing**, and maintaining overall database health. It works behind the scenes to ensure that the Oracle Database is always in a consistent and optimal state.