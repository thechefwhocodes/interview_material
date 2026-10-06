# Database

- Data stored persistently
- Data stored in hard drive
- Hard Drive stored in Database

## Indexes

- Finding specific row in DB -> O(n) time complexity
- Updating specific row in DB -> find a row and update -> O(n) time complexity
- Both reads and writes are O(n)
- For read operations, we want to support:
  - Search by specific key
  - Search by range query
- Indexes are needed to have reads better than O(n)

### Write Ahead Log (WAL)

- To improve write complexity
  - Write new rows at the end
  - O(1) time complexity for writes
  - Reads are still O(n) with more rows

### Hash Index

- Hash the key and find index at which the data exist
- For conflicts
  - Linked List chaining
  - Probing
  - Amortized time complexity is still O(1)
- Cons
  - Data is distributed all across the disk and not kept close to each other
  - Doesn't provide range query
  - Hash Index live in memory
    - Memory is expensive
    - Memory is not durable
      - Use WAL to rebuild the hash index
      - Replay all the changes

### B-Tree Index

### LSM Tree + SS Table

## ACID

## Two Phase Locking

## SQL vs NoSQL

## MySQL

## Postgres

## MangoDB

## Cassandra

## GraphDB

## Hadoop

## HBase

## MapReduce

## Others
