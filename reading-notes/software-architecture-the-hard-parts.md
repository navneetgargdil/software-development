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

### Data Ownership and Distributed Transactions

#### Joint Ownership

<table>
    <thead>
        <tr>
            <th>Technique</th>
            <th>Advantages</th>
            <th>Disadvantages</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>
                <dl>
                    <dt>Joint Ownership &ndash; Table Split</dt>
                    <dd>Breaks a single table into multiple tables so that each service owns a part of the data it's responsible for</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Preserves bounded context</li>
                    <li>Single data ownership</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Tables must be altered and restructured</li>
                    <li>Possible data consistency issues</li>
                    <li>No ACID transaction between table updates</li>
                    <li>Data synchronization is difficult</li>
                    <li>Data replication between tables may occur</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <dl>
                    <dt>Joint Ownership &ndash; Data Domain</dt>
                    <dd>Data ownership is shared between the services, creating multiple owners for the table</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Good data access performance</li>
                    <li>No scalability and throughput issues</li>
                    <li>Data remains consistent</li>
                    <li>No service dependency</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Data schema changes involve more services</li>
                    <li>Increased testing scope for data schema changes</li>
                    <li>Data ownership governance (write responsibility)</li>
                    <li>Increased deployment risk for data schema changes</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <dl>
                    <dt>Joint Ownership &ndash; Delegate</dt>
                    <dd>One service is assigned single ownership of the table and becomes the delegate, and the other service (or services) communicates with the delegate to perform updates on its behalf</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Forms single table ownership</li>
                    <li>Good data schema change control</li>
                    <li>Abstracts data structures from other services</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>High level of service coupling</li>
                    <li>Low performance for nonowner writes</li>
                    <li>No atomic transaction for nonowner writes</li>
                    <li>Low fault tolerance for nonowner services</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <dl>
                    <dt>Joint Ownership &ndash; Service Consolidation</dt>
                    <dd>Resolves service dependency and addresses joint ownership by combining multiple table owners (services) into a single consolidated service, thus moving joint ownership into a single ownership scenario</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Preserves atomic transactions</li>
                    <li>Good overall performance</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>More coarse-grained scalability</li>
                    <li>Less fault tolerance</li>
                    <li>Increased deployment risk</li>
                    <li>Increased testing scope</li>
                </ul>
            </td>
        </tr>
    </tbody>
</table>

#### Eventual Consistency Patterns

<table>
    <thead>
        <tr>
            <th>Pattern</th>
            <th>Advantages</th>
            <th>Disadvantages</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>
                <dl>
                    <dt>Background Synchronization Pattern</dt>
                    <dd>Uses a separate external service or process to periodically check data sources and keep them in sync with one another</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Services are decoupled</li>
                    <li>Good responsiveness</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Data source coupling
                    <li>Complex implementation</li>
                    <li>Breaks bounded contexts</li>
                    <li>Business logic may be duplicated</li>
                    <li>Slow eventual consistency</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <dl>
                    <dt>Orchestrated Request-Based Patten</dt>
                    <dd>Attempts to process the entire distributed transaction during the business request, and therefore requires some sort of orchestrator to manage the distributed transaction</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Slower responsiveness</li>
                    <li>Complex error handling</li>
                    <li>Usually requires compensating transactions</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Data schema changes involve more services</li>
                    <li>Increased testing scope for data schema changes</li>
                    <li>Data ownership governance (write responsibility)</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <dl>
                    <dt>Event-Based Pattern</dt>
                    <dd>Events are used in conjunction with an asynchronous publish-and-subscribe message model to post events or command messages to a topic or event stream</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Services are decoupled</li>
                    <li>Timeliness of data consistency</li>
                    <li>Fast responsiveness</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Complex error handling</li>
                </ul>
            </td>
        </tr>
    </tbody>
</table>

### Managing Distributed Workflows

#### Orchestration vs. Choreography Communication Styles

<table>
    <thead>
        <tr>
            <th>Communication Style</th>
            <th>Advantages</th>
            <th>Disadvantages</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>
                <dl>
                    <dt>Orchestration Communication Style</dt>
                    <dd>Has an orchestrator that manages workflow state, optional behavior, error handling, notification, and other workflows</dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Centralized workflow</li>
                    <li>Error handling</li>
                    <li>Recoverability</li>
                    <li>State management</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Responsiveness</li>
                    <li>Fault tolerance</li>
                    <li>Scalability</li>
                    <li>Service coupling</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <dl>
                    <dt>Choreography Communication Style</dt>
                    <dd>No central coordination; each service participates with others</dd>
                    <dd><a href="#choreography-strategies">Choreography strategies</a>:</dd>
                    <dd>
                        <ul>
                            <li>Front Controller pattern</li>
                            <li>Stateless choreography</li>
                            <li>Stamp coupling</li>
                        </ul>
                    </dd>
                </dl>
            </td>
            <td>
                <ul>
                    <li>Responsiveness</li>
                    <li>Scalability</li>
                    <li>Fault tolerance</li>
                    <li>Service decoupling</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Distributed workflow
                    <li>State management</li>
                    <li>Error handling</li>
                    <li>Recoverability</li>
                </ul>
            </td>
        </tr>
    </tbody>
</table>

#### Choreography Strategies

<table>
    <thead>
        <tr>
            <th>Choreography Strategy</th>
            <th>Advantages</th>
            <th>Disadvantages</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Front Controller pattern</td>
            <td>
                <ul>
                    <li>Creates a pseudo-orchestrator within choreography</li>
                    <li>Makes querying the state of an order trivial</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Adds additional workflow state to a domain service</li>
                    <li>Increases communication overhead</li>
                    <li>Detrimental to performance and scale as it increases integration communication chatter</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>Stateless choreography</td>
            <td>
                <ul>
                    <li>Offers high performance and scale</li>
                    <li>Makes querying the state of an order trivial</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Workflow state must be built on the fly</li>
                    <li>Complexity rises swiftly with complex workflows</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>Stamp coupling</td>
            <td>
                <ul>
                    <li>Allows domain services to pass workflow state without additional queries to a state owner</li>
                    <li>Eliminates need for a front controller</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Contracts must be larger to accommodate workflow state</li>
                    <li>Doesn't provide just-in-time status queries</li>
                </ul>
            </td>
        </tr>
    </tbody>
</table>

### Transactional Sagas

<table>
    <thead>
        <tr>
            <th>Technique</th>
            <th>Advantages</th>
            <th>Disadvantages</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>State management</td>
            <td>
                <ul>
                    <li>Good responsiveness</li>
                    <li>Less impact to end user for errors</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Data may be out of sync when errors occur</li>
                    <li>Eventual consistency may take some time</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>Atomic distributed transactions and compensating update</td>
            <td>
                <ul>
                    <li>All data restored to prior state</li>
                    <li>Allows retries and restart</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>No transaction isolation</li>
                    <li>Side effects may occur on compensation</li>
                    <li>Compensation may fail</li>
                    <li>Poor responsiveness for the end user</li>
                </ul>
            </td>
        </tr>
    </tbody>
</table>

### Contracts

<table>
    <thead>
        <tr>
            <th>Contract Type</th>
            <th>Advantages</th>
            <th>Disadvantages</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Strict contracts</td>
            <td>
                <ul>
                    <li>Guaranteed contract fidelity</li>
                    <li>Versioned</li>
                    <li>Easier to verify at build time</li>
                    <li>Better documentation</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Tight coupling</li>
                    <li>Versioned</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>Loose contracts</td>
            <td>
                <ul>
                    <li>Highly decoupled</li>
                    <li>Easier to evolve</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Contract management</li>
                    <li>Requires fitness functions</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>Consumer-driven contracts</td>
            <td>
                <ul>
                    <li>Allows loose contract coupling between services</li>
                    <li>Allows variability in strictness</li>
                    <li>Evolvable</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Requires engineering maturity</li>
                    <li>Two interlocking mechanisms rather than one</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>Stamp coupling</td>
            <td>
                <ul>
                    <li>Allows complex workflows within choreographed solutions</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Creates (sometimes artificially) high coupling between collaborators</li>
                    <li>Can create bandwidth issues at high scale</li>
                </ul>
            </td>
        </tr>
    </tbody>
</table>

### Managing Analytical Data

<table>
    <thead>
        <tr>
            <th>Pattern</th>
            <th>Main Characteristics</th>
            <th>Advantages</th>
            <th>Disadvantages</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Data Warehouse pattern</td>
            <td>
                <ul>
                    <li>Data extracted from many sources</li>
                    <li>Transformed to single schema</li>
                    <li>Loaded into warehouse</li>
                    <li>Analysis done on the warehouse</li>
                    <li>used by data analysts</li>
                    <li>BI reports and dashboards</li>
                    <li>SQL-ish interface</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Centralized consolidation of data</li>
                    <li>Dedicated analytics silo provides isolation</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Extreme partitioning of domain knowledge</li>
                    <li>Integration brittleness</li>
                    <li>Complexity</li>
                    <li>Limited functionality for intended purpose</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>Data Lake pattern</td>
            <td>
                <ul>
                    <li>Data extracted from many sources</li>
                    <li>Loaded into the lake</li>
                    <li>Used by data scientists</li>
                    <li>Difficulty in discovery of proper assets</li>
                    <li>PII and other sensitive data</li>
                    <li>Still technically, not domain, partitioned</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Offers high performance and scale</li>
                    <li>Makes querying the state of an order trivial</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Workflow state must be built on the fly</li>
                    <li>Complexity rises swiftly with complex workflows</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>Data Mesh pattern</td>
            <td>
                <ul>
                    <li>Domain ownership of data</li>
                    <li>Data as a product</li>
                    <li>Self-serve data platform</li>
                    <li>Computational federated governance</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Highly suitable for microservices architectures</li>
                    <li>Follows modern architecture principles and engineering practices</li>
                    <li>Allows excellent decoupling between analytical and operational data</li>
                    <li>Carefully formed contracts allow loosely coupled evolution of analytical capabilities</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Requires contract coordination with data product quantum</li>
                    <li>Requires asynchronous communication and eventual consistency</li>
                </ul>
            </td>
        </tr>
    </tbody>
</table>