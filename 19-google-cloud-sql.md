# 19. Google Cloud SQL

- Google becomes your DBA
- Google handles:
    - patch management
    - updates
    - replication
    - etc.
- allows developers to focus on the logical side, and creating data structures

## Cloud SQL

[Cloud SQL](https://cloud.google.com/sql/)

- Google-managed MySQL databases
- pay-per-use model without lock-in
- highly-available, reliable, secure
- supports standard MySQL connections
- seamless integration with GAE and GCE
- REST API for management

## Cloud SQL Features

- familiar
- flexible charging
- managed backups
    - retains 7 backups, included in initial cost
- connect from anywhere
- automatic replication
- fast connection from GCE and GAE
- Google security

## Interacting with Cloud SQL

- standard tools, such as MySQL client

    ```bash
    $ mysql --host=<instance-ip> --user=<user-name> --password
    ```
- using Google Cloud SDK

    ```bash
    $ gcloud sql instances create <instance-name>
    ```
    
## Comparing Storage Options

### Cloud Datastore

- capacity: terabytes+
- unit size: 1MB / entity
- storage type: NoSQL
- primary user case: use with App Engine

### Cloud Storage

- capacity: petabytes+
- unit size: 5TB / object
- storage type: Object storage
- primary user case: unstructured data

### Cloud SQL

- capacity: terabytes+
- unit size: 10TB
- storage type: Relational SQL
- primary user case: managed MySQL

Via [Google Stoarge options](https://cloud.google.com/storage-options/):

![Storage flow chart](./assets/19-flow-chart.svg)

