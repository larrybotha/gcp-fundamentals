# 8. Projects

- all GCP services are associated with a project
    - track resource usage and quota usage
    - enable billing
    - manage permissions and credentials
    - enables services and APIs
- projects use 3 identifying attributes:
    - project name
    - project number
    - project ID (or application ID)
        - manually created of auto-generated
        - must be unique across GCP
        - attribute used when working with applications and SDKs
- projects are interacted with using the developer console or Cloud Resource Manager API

## Google Cloud Shell

```bash
# show all projects
$ gcloud projects list
```

## Project Permissions

Projects can have multiple users of different permissions.

Users and permissions can be managed via [IAM & Admin](https://console.cloud.google.com/iam-admin/iam)

There are 4 roles of permission:

- owner
    - invite members
    - remove members
    - can delete project
    - includes editor rights
- editor
    - deploy applications
    - modify code
    - configure services
    - includes viewer rights
- viewer
    - read-only access
- billing administrator
    - manage billing
    - add administrators
    - remove administrators

### Adding non-billing users

Adding a user with `owner` role will send an invitation to confirm the role. Other roles get added to the project automatically.

### Adding billing users

Add members via the [Billing screen](https://console.cloud.google.com/billing)

## Google Cloud IAM

IAM = *I*dentity and *A*ccess *M*anagement

- IAM is used to create and manage project permissions
    - supports Google Genomics and Cloud Pub/Sub
    - accessible via developer console or IAM APIs
- create policies that assign users or groups to roles
    - roles provide access to resources
    - exposes additional flexible roles beyond owner, editor, and viewer
        - e.g. Pub/Sub exposes topic publisher / subscriber roles
- Cloud IAM supports granting permissions via
    - Google Groups
    - Google Domains
    - Google Accounts
    - Service Accounts
        
Each resource (e.g. Compute Engine, Pub/Sub, etc.) has a policy associated with it. A policy is a group of users, and the roles they are bound to.

Policies for a project can be viewed using the `get-iam-policy` command:

```bash
$ gcloud beta projects get-iam-policy [project-id]
```

We can see all the commands available (also to beta) for projects using the following command:

```bash
$ gcloud beta projects -h
Usage: gcloud beta projects [optional flags] <command>
  command may be         add-iam-policy-binding | delete | describe |
                         get-iam-policy | list | move |
                         remove-iam-policy-binding | set-iam-policy | undelete |
                         update
```

## Service Accounts

- provide an identity for carrying out server-to-server interactions in a project
    - e.g. Compute Engine running data, and needs to dump that data into a bucket or stream it to BigQuery. Because it's running in the background, a user can't be responsible for making the requests. Service accounts solve this by being available to authenticate requests
    - Compute Engine and App Engine service accounts are created by default (visit [IAM Admin](https://console.cloud.google.com/iam-admin/iam)
- used with Compute Engine, App Engine, and Cloud Monitoring
- used to authenticate from one service to another using view or edit permissions

## Project Best Practices

### Additional Owners

- a project with 1 owner will be deleted should that owner be deleted
- add at least one additional owner to prevent this from happening
- suspend owner accounts when they are no longer required, rather than deleting them


### Multiple Projects

- it's useful having multiple projects for different stages of a release pipeline
- users can be assigned to only the projects they need access to
