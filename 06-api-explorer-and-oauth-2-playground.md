# 6. API Explorer and OAuth 2.0 Playground

## API Explorer

[API Explorer](https://developers.google.com/apis-explorer)

### Demos

#### Translate API
Looking at the [Translate API](https://developers.google.com/apis-explorer/#search/translate/translate/v2/) there are a few services available:

- language detection
- lists of languages supported for translation
- translation

#### Language Detection

Under [language detection](https://developers.google.com/apis-explorer/#search/translate/translate/v2/language.detections.list):

- we are provided with fields to make requests
    - anything in red is required
- requests can be done via OAuth 2.0 or as unauthorised requests
- requests are sent via URL parameters:
    
    ```http
    GET https://translation.googleapis.com/language/translate/v2/detect?q=bonjour&key={YOUR_API_KEY}
    ```
- response data is JSON

#### Language Translation

Under [language translations](https://developers.google.com/apis-explorer/#search/translate/translate/v2/language.translations.list)

- there are additional fields including a 'fields' field:
    - allows one to customise output instead of receiving the entire response
    
#### Cloud Storage

[Cloud Storage APIs](https://developers.google.com/apis-explorer/#search/cloud%20storage/)

Creating a bucket via the API:

1. Make sure that the API is enabled under the current project under [APIs and Services](https://console.cloud.google.com/apis/dashboard)
    - Visiting [Cloud Storage](https://console.cloud.google.com/storage/browser) should show no buckets for a new project
2. In API explorer select the service for [inserting / creating a new bucket](https://developers.google.com/apis-explorer/#search/cloud%20storage/storage/v1/storage.buckets.insert)
3. Add fields
    - under 'Request body' there's a field with name but no value - these are required fields
    - add another field under 'Request body' for `storageClass`, and select from option suggested by the question mark
4. Creating a bucket has to be done with authorisation, as it will affect our project directly
    - a modal appears requesting which scopes are authorised for the bucket
5. We can see which scope are enabled for this API in the top-right question mark next to the OAuth toggler
6. Once the bucket is created
    - we can see the request and response
    - we can see the bucket in the [project](https://console.cloud.google.com/storage/browser)
    
To add to the bucket we can use gsutil:

```bash
$ gsutil cp *.png gs://[your-bucket-id]

Copying file://[my-files]...
| [1 files][ 28.4 MiB/ 28.4 MiB]    3.1 MiB/s
Operation completed over 1 objects/28.4 MiB.
```

## OAuth 2.0 Playground

[OAuth Playground](https://developers.google.com/oauthplayground/)

Find *Cloud Storage JSON API v1* and click to view the scopes available on that API.

To read from the bucket created:

1. select teh readonly scope
2. authorise
3. exchange authorisation code for tokens
