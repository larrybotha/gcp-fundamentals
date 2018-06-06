# 5. Interacting with Google Cloud Platform

## Ways of interacting with Google Cloud Platform

1. Web UI / Google Developer Console
2. Command line tool / Cloud SDK
3. REST API
4. Mobile app

## Google Developer Console

[console.cloud.google.com](https://console.cloud.google.com)

- centralised console for all project data
- manage and create projects:
    - billing
    - project membership
    - API activation
    - OAuth 2.0 credentials
    - service accounts
    - API keys
- developer tools
    - source code repo - free private Github repo
    - monitoring
    
## Cloud SDK

Everything in the command line tool is now available in the web shell inside the developer console. This allows people to bypass firewalls etc. that enterprise networks may enforce.

- cli tools for Linux, OSX, Windows
- available as a docker image
- install components to manage GCP services
    - `app` for App Engine
    - `bq` for BigQuery
    - `compute` for Compute Engine
    - `dns` for Cloud DNS
    - `gsutil` for Cloud Storage
    - `sql` for Cloud SQL
- App Engine SDKs are separate downloads
    - [cloud.google.com/appengine/downloads](https://cloud.google.com/appengine/downloads)

 ## REST APIs
 
 - typically use JSON
 - some paramaeters can be passed in as URL parameters
 - uses OAuth 2.0 protocol
    - authentication
        - you are who you say you are
    - authorisation
        - you can access what you are authorised to access
    - Google make OAuth 2 easy, as many devs struggle to wrap their heads around it
        - before OAuth, to access a user's Google data via an app:
            - store user's data on app
            - app impersonates user to make requests
        - with OAuth, to access data via an app
            - OAuth uses a 3-legged flow
                - application requests from Google a scope of access, e.g. get mails from Gmail
                - Google redirects user to login and consent screen
                - use either grants app permissions or not
                - if user accepts, then app can request data from Google without storing any credentials
- enabled through Google Developers Console
    -   ```bash
        $ gcloud compute regions list
        # if error, then compute has not been enabled for the current project
        
        # get current project info
        $ glcoud info
        ```
    - to enable an API:
        - click on service, e.g. 'Compute' in sidebar in Google Developers Console, and the API will be automatically enabled or
        - select an API to enable from the [APIs library](https://console.cloud.google.com/apis/library)
- most APIs include daily quotas and limits, and products include their own quotas
    - quotas can be viewed at [console.cloud.google.com/iam-admin/quotas](https://console.cloud.google.com/iam-admin/quotas)
    - quotas have soft-limits - Google can be contacted to bump up restrictions
    - GCE allows for 100 firewall rules per product
    - plan ahead to manage capacity requirements
    - [all GCP services in 4 words or less](https://github.com/agasthik/GoogleCloudArchitectProfessional/blob/master/GCP-Products-Cheat-Sheet.md)
    
## Google API Client Libraries

 Wrappers around APIs making it easier to integrate with services.
 
 - supports many languages
    - java, python, Node, JS, PHP, .NET, ObjectiveC, Go, etc.
 - client libraries handle auth via OAuth 2.0
        1. in developers console generate credentials
        2. Google provides client ID and secret
        3. provide credentials to library

### Client Library Workflow

1. request user's credentials
    - kicks off OAuth 2.0 "flow"
    - "flow" may ask for user's login info
    - [OAuth Playground](http://developers.google.com/oauthplayground)
        - great for understanding what's going on under the hood
        - can see requests and responses
        - can see authorisation codes and access tokens
        - select which scope you want to access
2. authorise HTTP object with credentials
    - provide client ID and secret
3. create service API object / client with the authorised HTTP object from which we make calls to services

More can be read at [developers.google.com/identity/protocols/OAuth2](https://developers.google.com/identity/protocols/OAuth2)
