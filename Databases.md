# Databases

| Name          | Type        | PACELC | Consensus | Use Cases                                                 |
| ------------- | ----------- | ------ | --------- | --------------------------------------------------------- |
| Cassandra     | Wide-Column | PA/EL  | Quorum    | High write throughput, time-series, distributed workloads |
| CockroachDB   | SQL         | CP/EL  | Raft      | Financial systems, globally distributed apps              |
| Couchbase     | Document    | PA/EL  | Quorum    | Caching, mobile sync, user profiles                       |
| Elasticsearch | Search      | PA/EL  | Quorum    | Full-text search, logging, observability                  |
| FoundationDB  | Key-Value   | CP/EC  | Paxos     | Financial systems, building DB layers                     |
| MongoDB       | Document    | PA/EL  | Raft-Like | Web apps, flexible schema, content systems                |
| Neo4j         | Graph       | CA/EC  | Raft      | Recommendations, fraud detection, graph traversal         |
| PostgreSQL    | SQL         | CA/EC  | None      | General-purpose, OLTP, analytics                          |
| Redis         | Key-Value   | PA/EL  | None      | Caching, sessions, real-time data                         |
| ScyllaDB      | Wide-Column | PA/EL  | Quorum    | High-performance Cassandra workloads                      |
| SpannerDB     | SQL         | CP/EC  | Paxos     | Global transactions, strongly consistent systems          |
| YugabyteDB    | SQL         | CP/EL  | Raft      | Cloud-native, global transactional systems                |

## CAP and PACELC

### CAP Theorem

The CAP theorem states that in a distributed system, you can only guarantee **two out of three properties** simultaneously:

- **C** = Consistency: Every read receives the most recent write.

- **A** = Availability: Every request receives a response, even if it’s not the most recent.

- **P** = Partition Tolerance: The system continues to operate despite network partitions.

### PACELC Theorem

PACELC extends CAP by considering latency trade-offs **even when there is no partition**:

- **P** = Partition Tolerance (same as CAP)

- **A/C** = Trade-off during a Partition: choose between Availability or Consistency

- **E/L** = Trade-off when system is **Else** (no partition): choose between Latency or Consistency
