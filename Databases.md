# Databases

| Name          | Type        | PACELC | Consensus | Use Cases                                                 |
| ------------- | ----------- | ------ | --------- | --------------------------------------------------------- |
| Cassandra     | Wide-Column | PA/EL  | Paxos     | High write throughput, time-series, distributed workloads |
| CockroachDB   | SQL         | PC/EC  | Raft      | Financial systems, globally distributed apps              |
| Couchbase     | Document    | PC/EC  | None      | Caching, mobile sync, user profiles                       |
| Elasticsearch | Search      | PC/EL  | Raft      | Full-text search, logging, observability                  |
| FoundationDB  | Key-Value   | PC/EC  | Paxos     | Financial systems, building DB layers                     |
| MongoDB       | Document    | PA/EC  | Raft      | Web apps, flexible schema, content systems                |
| Neo4j         | Graph       | PC/EC  | Raft      | Recommendations, fraud detection, graph traversal         |
| PostgreSQL    | SQL         | PC/EC  | None      | General-purpose, OLTP, analytics                          |
| Redis         | Key-Value   | PA/EL  | None      | Caching, sessions, real-time data                         |
| ScyllaDB      | Wide-Column | PA/EL  | Raft      | High-performance Cassandra workloads                      |
| SpannerDB     | SQL         | PC/EC  | Paxos     | Global transactions, strongly consistent systems          |
| YugabyteDB    | SQL         | PC/EC  | Raft      | Cloud-native, global transactional systems                |

## CAP Theorem

The CAP theorem states that during a **network partition**, a distributed system can guarantee only **two of these three properties** at the same time:

- **C** = Consistency: every read receives the most recent write (linearizability, which is not the same as the "C" in ACID).

- **A** = Availability: every request receives a non-error response, even if it is not the most recent.

- **P** = Partition Tolerance: the system keeps operating despite lost or delayed messages between nodes.

In practice, partitions are unavoidable in a distributed system, so **P is mandatory** and the real decision is **C vs A while the partition lasts**. A "CA" system is effectively a **single node**, which simply becomes unavailable under a partition.

## PACELC Theorem

PACELC extends CAP by also describing the trade-off **when there is no partition**. It reads as a conditional:

> **if** there is a Partition (**P**) → choose between **A**vailability and **C**onsistency; **E**lse (**E**), with no partition → choose between **L**atency and **C**onsistency.

Here **P is a condition** ("if partitioned"), not a guaranteed property as in CAP. This produces four categories:

- **PA/EL** — available under partition, low latency otherwise.

- **PA/EC** — available under partition, consistent otherwise.

- **PC/EL** — consistent under partition, low latency otherwise.

- **PC/EC** — consistent in both cases.
