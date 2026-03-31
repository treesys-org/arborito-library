@title: DFS Namespaces and Replication
@icon: 🌳
@description: Unifying and replicating file shares.
@order: 4

# DFS Namespaces and DFS Replication

**DFS Namespace** provides a single UNC (`\\domain\public`) with multiple **folder targets**. **DFS Replication** synchronizes content between servers (multi-master with conflict resolution).

@section: Namespace benefits

Users keep one UNC while you migrate servers behind the scenes by updating targets.

@section: DFS-R

*   Requires healthy **AD replication**.
*   Define topology (full mesh vs hub/spoke).
*   **Not a backup**—it is replication, not snapshot backup.

@section: Monitoring

DFS Management console and **DFS Replication** event logs.

@quiz: Which DFS component provides a single logical UNC with multiple targets?
@option: DFS Replication
@correct: DFS Namespaces
@option: WSUS

@quiz: What is a key warning about DFS Replication?
@option: It replaces backups
@correct: It replicates files but does not replace enterprise backup/restore strategy
@option: It only works on Windows XP
