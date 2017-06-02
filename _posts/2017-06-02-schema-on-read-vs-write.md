---
layout: post
title: Schema on Read vs Schema on Write
---

# Schema on Write
## You have to define the schema before you write data into system
## Used in traditional RDBMS
## Pros
- extremely useful in expressing relationships between data points
- quick to read and write data
## Cons
- schemas are typically purpose-built and hard to change, while type of data may change
- considerable modeling/implementation effort

# Schema on Read
## Store the data without schema and determine the schema when you read it
## Supported in HIVE
## Pros
- massive flexibility over how data can be consumed
- raw/atomic data can be stored to reference and consumption years into the future
- flexibility to store unstructured, semi-structured, and loosely or unoriganized data
## Cons
- data is not self-documenting
- can be expensive in terms of compute resources
- have to spend time creating the jobs that create the schema on read
