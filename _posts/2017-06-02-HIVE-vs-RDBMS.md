---
layout: post
title: Hive vs RDBMS
category: big data
tags: big data
---

- Hive support **100s of PB** data while RDBMS supports **10s of TB** data
- Hive enforces **schema on read** while RDBMS enforces **schema on write**
- As Hadoop is batch-oriented system, Hive does not support **OLTP(Online Transaction Processing)** and it is close to **OLAP(Online Analytics System)**. RDBMS supports OLTP.
- Hive is easier to scale than RDBMS
- RDBMS supports record level update, delete and inserts, transactions and indexes, which are not allowed in Hive. Hive was built to operate over HDFS data using MapReduce, where **full-table scan** are norm and a table update is achieved by transforming data into a new table
- Hive is designed for **write once and read many times** while RDBMS is designed for **read and write many times**.