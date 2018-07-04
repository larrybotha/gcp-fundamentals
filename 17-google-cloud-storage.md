# 17. Google Cloud Storage

## Cloud Storage

[Cloud Storate](https://cloud.google.com/storage)

- high performance, internet-scale, immutable blob storage
    - files can't be changed, only added, removed, or overwritten
- not a mountable file system on physical or virtual machines
- simple administration and does not require capcacity management
- all storage options accessed through the same APIs and include client libraries
    - JSON API
    - XML API
    
## Cloud Storage Options

When creating a bucket we need to specify three things:

- name (need to be unique across Cloud Storage)
- specify a location (region / multi-regional)
- storage class

### High-performance Storage

- provides highest durability, availability, and performance
- low latency
- ideal for 
    - use with website content distribution
    - video streaming
- can be regional or multi-regional

### Backup and Archival Storage

- low cost durable storage
- used for:
    - archiving
    - online backup
    - disaster recovery
- don't have long wait times to retrieve / access data

#### Nearline Storage

Low frequency access storage

- for data accessed less than once per month

#E## Coldline Storage

Lowest frequency access storage

- for data accessed less than once per year

## Cloud Storage Components

- data is organised into buckets which are configured with a storage class (region, multi-regional, nearline, coldline)
- all buckets shre a global namespace
- create and manage multiple buckets within a project
- buckets are used to store objects
- objects are comprised of data and metadata
- buckets cannot be nested inside other buckets
- objects cannot be nested inside other objects (folders can be simulated by adding `/` to an object's name)

 ## Cloud Storage Sharing and Collaboration
 
- administrative access to buckets is typically shared using GCP projects permissions
- all buckets and objects have default ACLs (**A**ccess **C**ontrol **L**ists)
    - when a bucket is created it gets a default ACL from configurable defaults
- can modify ACLs to allow for public web access
    - scopes can be specified for objects, e.g. `all users`
- ACLs use Google Accounts
    - allows one to grant specific levels of permission:
        - read/write/owner for buckets
        - read/owner for objects
- can also generate signed URLs for anonymous access
    - can create access for a limited amount of time
    
        ```bash
        # create a signed URL that:
        # grants access to a file via the GET method (-m)
        # for a duration of 1 minute (-d)
        # for object found in the specified bucket
        $ gsutil signurl -m GET -d 1m gcs-sa.json gs://<bucket-name>/kitten.png
        ```
    - can be done for reading, writing, and deleting objects


## `gsutil`

- cloud SDK includes the `gsutil` cli for managing GS resources
- buckets use the URI scheme `gs://`
- used to work with buckets and objects

    ```bash
    # return the buckets in a project
    $ gsutils ls
    gs://exported-data
    gs://startup-scripts
    
    # return the objects within a bucket
    $ gsutils ls startup-scripts
    gs://startup-scripts/startup.sh
    gs://startup-scripts/shutdown.sh
    
    # copy the objects from a bucket to a specified location (i.e. local machine for this command)
    $ gsutils cp gs://exported-data/*.json .
    Downloading files...
    ```
    
## Cloud Storage Features

- website use
    - content delivery
    - can run static sites from a bucket
- regional buckets
    - improve perf. for users in a specific region by configuring a bucket for that region
- object lifecycle management
    - create policies for Cloud Storage to perform actions on objects baed on conditions. e.g.:
        - delete objects if they are over a certain age
        - delete objects created before a specific date
- object versioning
    - when enabled, all objects in a bucket will maintain a history
- online cloud import (Cloud Storage Transfer Service)
    - allows one to bring data into Cloud Storage:
        - from S3
        - from another Cloud Storage bucket
        - from a list of URLs
- Offline import (third party)
    - send data to third parties who will handle upload
    - useful for slow / expensive internet connections
- Object change notification
    - allow devs to create webhooks for objects and get notified of changes

## Cloud Storage Integration

- upload / download between different resources:
    - private computer
    - import / export tables in BigQuery
    - object storage and logs export with App Engine
    - startup scripts, images, and general object storage for Compute Engine
    - import / export tables for Cloud SQL
