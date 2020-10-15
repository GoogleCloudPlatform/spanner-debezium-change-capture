# Change log capture from Cloud Spanner to BigQuery using Debezium/Kafka-Connect

This is not an officially supported Google product.

Copyright 2020 Google LLC

## Introduction

This repository implements polling based change-capture by polling values from
Cloud Spanner based on record's commit timestamp.

The poller runs at a fixed frequency and captures changes which have happened
since the last time the database was polled, using a query of the form:

```
select * from table where LastUpdateTime > last_poll_timestamp
```

## Considerations and limitations

Consider the following system design considerations and limitations before
deploying this solution.

### Performance

Appropriate for incremental, batch replication with moderate write traffic (upto
few thousand upserts per second).

### High availability & fault tolerance

Supported. This solution runs Kafka Connect in distributed mode. Connector jobs
are distributed across the cluster providing high availability and fault
tolerance. Learn more about how Kafka handles failures.

### Dynamic schema updates

Supported.

### Transaction order preservation

Not supported.

### Deduplication/Exactly once delivery guarantees

Not supported. Supports append-only replication, where newly replicated data is
appended to the end of a table. Existing rows are not updated - updates are
added to the end of the table as new rows.

### Deletes

Not supported. Supports soft deletes or tombstone in the form of row updates.

### Impact on data schema

All tables that need to be replicated must have a column which makes use of
Spanner's commit timestamp to record last updated time.

### Impact on existing application

Any modification of the data in the tables that need to be replicated need to
set/update the Spanner commit timestamp. If the application deletes rows, then
this must be changed to soft-delete or tombstone as indicated above.

### Support for sinks

Kafka Connect provides flexibility of changing the data sink system at any time
without having to change any stream processing code. Learn more about supported
sinks in Kafka Connect.

## Setup

Steps to run the setup are covered in the
[Change log capture from Cloud Spanner to BigQuery using Debezium tutorial](https://cloud.google.com/solutions/capturing-change-logs-with-debezium)
