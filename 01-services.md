# 1. GCP Services

GCP includes IaaS and PaaS. SaaS, such as Gmail, fall outside of the scope of GCP.

## IaaS

1. Takes care of the hardware level.
2. Requires more overhead as you're responsible for creating and managing instances etc.
3. Pay only for what you use.

- Compute Engine
    - raw computing
    - granular control
- Container Engine
    - datacenter as computer
    - declarative management

## PaaS

1. Takes care of both the hardware and software, e.g. runtimes, dependencies, redundancy etc.
2. Write code and go - focus on app logic by making use of pre-set runtimes.
3. Pay only for what you use.

- App Engine
    - pre-set runtimes
    - focus on app logic
- Cloud Endpoints
- Manage VMs
    - bring your own runtime
    - health-checked VMs 
    
## Compute Services
    
### Compute Engine

Sort of like building a real pc, but through an API instead of physical components.

- run large-scale workloads on Google's VMs
- robust networking features
- instance metadata & startup scripts
- high CPU, high memory, standard, & shared-core machines
- persistent disk snapshots
- APIs for auto-scaling & group management

### Container Engine

Container is Kubernetes and Docker

- based on Kubernetes
- orchestrate & schedule docker containers
- consumes Compute Engine instances
- uses declarative syntax to manage applications
- manages and maintains:
    - logging
    - health management
    - monitoring
    - scaling
    
### App Engine

Focus on features rather than infrastucture.

- managed runtimes for specific versions of python, node, Go, etc.
- autoscale web workloads to meet demand
- free daily quotas and usage-based pricing
- local SDK for dev and deployment
- must conform to sandbox constraints:
    - no writing to local fs
    - request timeouts at 60s
    - limit on 3rd-party software installations
    
## Storage Services

### Cloud Bigtable

- NoSQL db service for web, mobile, and large-workload apps
    - starts at terabyte, scales up to petabyte range
- integrated
    - accessed using Apache HBase API
    - native comppatbility with big data ecosystem - let Google do the heavy lifting
- protected
    - replicated storage
    - data encryption
- proven
    - drives many major Google apps

### CLoud Storage

Useful for storing backups and archiving data - where most people get started.

- high performance immutable blob storage
- not a file system (Fuse can be used to treat it as such)
- simple administration and does not require capacity management
- all storage options accessed through the same APIs and include client libs:
    - JSON API
    - XML API

### Cloud SQL

Fully managed SQL databases

- Google-managed SQL
- pay-per-use model
- REST API for management
- used for affordability and performance
- Google security
- Vertical scaling (reading and writing)
- Horizontal scaling (reading)
- seamless integration with GAE and GCE

### Cloud Datastore

- NoSQL store for billions of rows
- Schemaless access - no need to think about underlying data structure
- local dev tools
- auto-scaling and fully managed
- built-in redundancy
- support for ACID transactions
- includes free daily quota
- access from anywhere via RESTful interface

### Bigtable vs Datastore

[Bigtable - what it's good for](https://cloud.google.com/bigtable/docs/overview#what-its-good-for)

[Cloud Datastore - what it's good for](https://cloud.google.com/datastore/docs/concepts/overview#what_its_good_for)

## Big Data

### BigQuery

- near real-time interactive analysis of massive datasets (Tb's of data in seconds)
- columnar storage for high performance
- queries using SQL-like syntax
- only pay for storage and processing
- zero admin for perf and scale
- supports open standards

### Cloud Pub/Sub

- scalable and reliable messaging
- supports many-to-many async messaging
- includes support for offline consumers
- based on proven Google tech
- integrates with Cloud Dataflow for data processing pipelines

### Cloud Dataflow

- construct scalable and reliable data pipelines
- executes data processing on GCE instances
- provices support for
    - ETL
    - analytics
    - real-time computations
    - process orchestration
- integrates with GCP services
    - Cloud Storage
    - Cloud Pub/Sub
    - BigQuery
    
### Cloud Datalab

Get insights from your data

- interactive tool for large-scale data exploration, transformation, analysis, and visualisation
    - analyze data in GBQ, GCE, GCS
    - easily deploy analyis models for GBQ
- integrated and open source
    - runs on App Engine
    - built on Jupiter
    - use Google Charts for easy visualisations
- code, docs, results, and visualisations combine in an intuitive notebook format

### Cloud Dataproc

- fast, easy, managed way to run Hadoop and Spark on GCP
- benefit from cloud integration
    - Cloud Storage
    - Cloud Monitoring
    - Cloud Logging
- Dataproc clusters are billed minute-by-minute
- any-time scaling - manually scale clusters even while jobs are running
- RESTful API and Cloud SDK integration

## App Services

### Cloud Endpoints

- build your own API running on App Engine
- expose your API using a RESTful interface
- includes support for OAuth 2.0
- Generate client libraries
- includes some App Engine features:
    - scaling
    - denial of service protection
    - high availability
- supports iOS, Android, and JS clients

###  Translate API

- translate arbitrary string between thousands of language pairs
- programmatically detect a language
- support for many languages
- supports standard Google API client libs

### Prediction API

- pattern-matching and machine-learning
- predict trends based on historical data
- use cases include spam detection and product recommendations
- data replicated using Cloud Storage
- integrates with other GCP services
    - App Engine
    - BigQuery
    - Cloud Storage
    
 ## Networking
 
 ### Cloud DNS
 
 - create and manage DNS programmatically
 
 ### HTTP(S) Load Balancing
 
 - balance HTTP-based traffic across multiple GCE regions
 - simple DNS setup
 - scalable - requires no pre-warming
 
 ### Network Load Balancing
 
 - spread TCP & UDP traffic over a pool of instances within a region
 - only healthy instances handle traffic
 - scalable, requires no pre-warming
 
 ## Cloud Management Services
 
 ### Cloud Monitoring
 
 - dashboards and alerts for cloud apps
 - review metrics fro cloud services, VMs, and commong open source apps
 
 ### Cloud Launcher
 
 - launch software packages on GCE in seconds
 - WordPress, LAMP, Jenkins etc.
 
 ### Cloud Deployment Manager
 
 Declarative management
 
 - infrastructure management service
 - create a template describing your environment and use deployment manager to create resources
 
 ### Cloud Logging
 
 - collect and store logs from GCE and GAE
 - view logs with log viewer
 - export logs to GCS and GBQ
