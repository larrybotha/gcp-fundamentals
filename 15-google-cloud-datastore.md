# 16 - Google Cloud Datastore

- Datastore eliminates the need for a DBA. Google takes care of:
    - high availability
    - scalability
    - redundancy
    - replication
    - sharding
- schemaless database

## Datastore

[Cloud Datastore](https://cloud.google.com/datastore/)

- NoSQL store for billions of rows
- schemaless access - no need to think about underlying data structure
- local development tools
- automatic scaling and fully managed
- built-in redundancy
- support for ACID transactions
- includes a free daily quota
- access from anywhere via REST API

## Kinds, Entities, Properties, and Keys

### Kind

- categorises entities for querying purposes
- corresponds to a table in a relational DB

### Entity

- data object containing properties
- corresponds to a row in a relational DB (and document in MongoDB)

### Property

- data values within an entity
- corresponds to a field in a relational DB

### Key

- identifier for the entity
- corresponds to a primary key in a relational DB

## Entity Groups

- entities can be organised into groups
- can include different types of entities
- an entity group is a set of entities connected through an ancestry, forming a heirarchical structure
- sequence of entities from parent to child to a specific entity is known as its ancestor path
- entity groups are required for achieving:
    - strong consistency, and 
    - for participating in transactions
    
## Datastore Development

- datastore client libraries:
    - on GAE: Java, Python, Go, Node
    - not on GAE: whatever you're willing to build / can find
- queries
    - supports filters, sorts, and special queries
    - familiar SQL-like language - GQL
    - most queries are eventually consistent
- indexes
    - all queries served by pre-built indexes
    - every property is automatically indexed
    - can define  custom indexes
    - performance scales with size of result set, not the size of the dataset
    
Indexes are store using Google's BigTable.
