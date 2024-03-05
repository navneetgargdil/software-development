# [_Software Architecture: The Hard Parts_](../books/Software_Architecture_The_Hard_Parts_(2021).pdf) Reading Notes

## Databases

### Adoption Characteristics of Different Types of Databases

| Rating Subject | Relational Databases | Key-Value Databases | Document Databases | Column Family Databases | Graph Databases | New SQL Databases | Cloud Native Databases | Time-Series Databases |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Ease of learning** | ★★★★ | ★★★ | ★★★ | ★★ | ★ | ★★★ | ★★ | ★ |
| **Ease of data modeling** | ★★★ | ★ | ★★★ | ★ | ★★ | ★★★ | ★★ | ★★ |
| **Scalability/throughput** | ★★ | ★★★★ | ★★ | ★★★★ | ★★★ | ★★★ | ★★★★ | ★★★★ |
| **Availability/partition tolerance** | ★ | ★★★★ | ★★★ | ★★★★ | ★★★ | ★★★ | ★★★ | ★★ |
| **Consistency** | ★★★★★ | ★★ | ★★ | ★ | ★★★ | ★★ | ★★★ | ★★★ |
| **Programming language support, product maurity, SQL support, community** | ★★★★ | ★★★ | ★★★ | ★★ | ★★ | ★★ | ★★ | ★★ |
| **Read/write priority** | `---o---` | `-o-----` | `-o-----` | `-----o-` | `-o-----` | `---o---` | `-o-----` | `-o-----` |