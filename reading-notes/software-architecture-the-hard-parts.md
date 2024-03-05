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
| **Read/write priority<br/>(← R \| W →)** | `---o---` | `-o-----` | `-o-----` | `-----o-` | `-o-----` | `---o---` | `-o-----` | `-o-----` |

### Database Types & Products

| Database Type | Products |
| ------------- | -------- |
| Relational |PostgreSQL, Oracle, Microsoft SQL |
| Key-Value | Riak KV, Amazon DynamoDB, Redis |
| Document | MongoDB, Couchbase, AWS DocumentDB |
| Column family | Cassandra, Scylla, Amazon SimpleDB |
| Graph | Neo4j, Infinite Graph, Tiger Graph |
| NewSQL | VoltDB, ClustrixDB, SimpleStore (aka MemSQL) |
| Cloud native | Snowflake, Datomic, Redshift |
| Time-series | InfluxDB, kdb+, Amazon Timestream |

## Trade-off Analyses

### Reuse Patterns

<table>
    <thead>
        <tr>
            <th>Technique</th>
            <th>Advantages</th>
            <th>Disadvantages</th>
            <th>When to Use</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>
                <dl>
                    <dt>Code Replication</dt>
                    <dd>Shared code is copied into each service, avoiding code sharing</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Preserves the bounded context</li>
                    <li>No code sharing</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Difficult to apply code changes</li>
                    <li>Code inconsistency across services</li>
                    <li>No versioning capabilities across services</li>
                </ul>
            </td>
            <td>Simple static code that is one-off or unlikely to ever change because of defects or functional changes</td>
        </tr>
        <tr>
            <td>
                <dl>
                    <dt>Shared Library</dt>
                    <dd>External artifact containing source code used by multiple services, bound to each service at compile time</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Ability to version changes</li>
                    <li>Shared code is compile-based, reducing runtime errors</li>
                    <li>Good agility for code shared code changes</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Dependencies can be difficult to manage</li>
                    <li>Code duplication in heterogeneous codebases</li>
                    <li>Version deprecation can be difficult</li>
                    <li>Version communication can be difficult</li>
                </ul>
            </td>
            <td>Homogeneous environments where shared code is low to moderate</td>
        </tr>
        <tr>
            <td>
                <dl>
                    <dt>Shared Service</dt>
                    <dd>Place shared functionality into a separately deployed service</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Good for high code volatility</li>
                    <li>No code duplication in heterogeneous codebases</li>
                    <li>Preserves the bounded context</li>
                    <li>No static code sharing</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Versioning changes can be difficult</li>
                    <li>Performance is impacted due to latency</li>
                    <li>Fault tolerance and availability issues due to service dependency</li>
                    <li>Scalability and throughput issues due to service dependency</li>
                    <li>Increased risk due to runtime changes</li>
                </ul>
            </td>
            <td>Highly polygot environments (multiple heterogeneous languages and platforms) and also when shared functionality tends to change often</td>
        </tr>
        <tr>
            <td>
                <dl>
                    <dt>Sidecar and Service Mesh</dt>
                    <dd>Decouples the domain logic from the technical (infrastructure) logic</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Offers a consistent way to create isolated coupling</li>
                    <li>Allows consistent infrastructure coordination</li>
                    <li>Ownership per team, centralized, or some combination</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Must implement a sidecar per platform</li>
                    <li>Sidecar component may grow large/complex</li>
                </ul>
            </td>
            <td>A clean way to spread some sort of cross-cutting concern across a distributed architecture; architectural equivalent to the Decorator Design Pattern</td>
        </tr>
    </tbody>
</table>