# Write path
---

Cassandra is a log structured engine

- Memtables – in-memory tables corresponding to CQL tables with indexes – changes can happen
- CommitLog – append-only log, replayed to restore downed node’s Memtables
- SSTable – Memtable snapshots periodically flushed to disk, cleaning heap – immutable
- Compaction – periodic process to merge and streamline SSTable

When a node receives a request – no disk seek necessary during write as everything gets written sequentially

- appends to the CommitLog and to the Memtable for this record’s target CQL table
- Periodically, Memtable flush to SSTables, clearing JVM heap and CommitLog
- Periodically, Compaction runs to merge and streamline SSTable

Configuration

- commitlog_total_space_in_mb (default: 1024)
- commitlog_segment_size_in_mb (default: 32)
- commitlog_directory – best practice is to locate on its own disk to minimize write head movement or on SSD
- commitlog_sync (default: periodic) – batch is the alternative
	- batch: commitlog_sync_batch_window_in_ms (default: 50) – how long to wait for more writes before fsync
	- periodic: commitlog_sync_period_in_ms (default: 10000) – how long to wait between fsync of log to disk – 10 second latency is ok considering the replications on other nodes
- memtable flushes when
	- memtable_total_space_in_mb is reached (default 25% of heap)
	- commitlog_total_space_in_mb is reached
	- “nodetool flush” is issued

SSTable – sorted string table

- an immutable file of sorted partitions
- written to disk through sequential IO
- contains the state of a Memtable when flushed
- CQL table = corresponding Memtables + SSTables
- periodically compacted from many to one
- structure fo SSTable
	- partition index
	- partition summary

Data directories

- created by keyspace and table_name-table_id

    …/data/keyspace/tablename-tableid
- Memtable flush or compaction generates the following:
	- -CompressionInfo.db: metadata for Data file compression
	- -Data.db: base SSTable
	- -Filter.db: SSTable partition keys Bloom Filter to optimize reads
	- -Index.db: index for this SSTable
	- -Statistics.db:stats for this SSTable
	- -Summary.db: sampling from index file
	- -TOC.db: component list for this SSTable
- sstable2json: exports SSTable in JSON



---
