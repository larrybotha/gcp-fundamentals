# 13. Google App Engine

Focus on code, not infrastructure.

- managed [runtimes](https://cloud.google.com/appengine/docs/) for specific versions of Java, PHP, Python, Go, etc.
- autoscale web workloads to meet demand
- free faily quota; usage-based pricing
- local SDK for development, testing, and deployment
- need to conform to sandbox constraints (only applicable to managed environments):
    - no writing to local filesystem
    - request timeouts at 60s
    - limit on 3rd party software installations
    
## App Engine Workflow - web apps

1. develop & test web application locally
2. use SDK to upload application to App Engine
3. App Engine automatically scales and reliably serves web app

### Built-in App Engine Services

[App Engine features](https://cloud.google.com/appengine/docs/standard/#index_of_features_

- users
    - allow applications to sign in users with Google Accounts using OAuth2.0
- modules
    - factor applications into logical components
    - scale modules individually
- task queue
    - perform work using small tasks executed outside of a user request
    - work can be queued outside of the 60s timeout limitation
- sockets
    - supports outbound sockets from within your application
- search
    - perform Google-like searches over structured data
- URL fetch
    - use Google's network to make HTTP/S requests to URLs
- logs
    - API access to application and request logs from app
- mail
    - send and receive email from application
- XMPP
    - send and receive chat messages from application
    
#### Memcache

Memcache is not a replacement for a database.

- cache read-heavy operations
    - database values
    - tokens
    - API calls
- key-value in-memory data-storage
- improves performance and reduces costs
- volatile, handle misses gracefully

Usage pattern:

1. application receives a query
2. application checks if data is in Memcache
    - if in memcache, retrieve that data
    - if not, application queries database, and stores result in memcache for future requests
    
## Comparing Compute Options

- Compute Engine
    - languages: any
    - service modal: IaaS
    - primary use case: general computing
- Container Engine
    - languages: any
    - service modal: hybrid
    - primary use case: container-based workloads
- App Engine:
    - languages: Java, Python, Go, etc.
    - service model: PaaS
    - primary use case: web & mobile apps
    
## Flexible Environment

[App Engine Flexible Environment](https://cloud.google.com/appengine/docs/flexible/)

- standard runtimes for Python, Java, Go, etc. without sandbox constraints
- custom runtime support for languages supporting HTTP requests
- pricing based on underlying GCE usage
- local development relies on Docker
- standard runtimes can access GAE services such as Datastore, Memcache, Task Queues, etc.

## Web UI

[App Engine Web UI](https://console.cloud.google.com/appengine)

- dashboard for metrics etc.
- services for managing services your app uses
- versions for deploying different versions of your app
- instances for instances hosting application
- etc.
