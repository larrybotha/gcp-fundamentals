# 18. Getting started with Cloud Storage

We will:

1. deploy an app to App Engine
2. enable Cloud Storage
3. create a bucket
4. use the Cloud Storage browser to view objects in the bucket

## Create a bucket via web UI

1. visit [Storage](https://console.cloud.google.com/storage/browser)
2. create a bucket
    1. select regional or multi-regional (perhaps the one closest to where your app will be served)
3. in the next view one can:
    1. upload files
    2. upload a folder (files will be prepended with `[folder-name]/`
    3. create a folder
4. at the [Storage Browser](https://console.cloud.google.com/storage/browser) permissions can be updated for the bucket

## Createa= a bucket via Cloud Shell

1. open Cloud Shell
2. create the bucket

    ```bash
    # make a bucket (mb)
    # with a class (-c) of nearline
    # at the location (-l) <region>
    # with a name of <unique-bucket-name>
    $ gsutil mb -c nearline -l <region> gs://<unique-bucket-name>
    ```

## Clone our app

1. visit [Compute Engine](https://console.cloud.google.com/compute/instances) and start up your instance
2. open Cloud Shell
3. ssh into the instance

    ```bash
    $ gcloud compute ssh <instance-name> --region<region>
    ```
4. clone the Cloud Storage app

    ```bash
    $ git clone https://github.com/GoogleCloudPlatformTraining/cp100-appengine-cloudstorage-python.git
    $ cd cp100-appengine-cloudstorage-python
    ```
5. view the files
    
    ```bash
    $ ls
    app.yaml  cloudstorage  favicon.ico  guestbook.py  index.html  index.yaml  LICENSE  README.md
    ```
    
    In addition to the files in the previous app, we have a `cloudstorage` folder containing the client library for interacting with Cloud Storage

```python
import jinja2
import os
# the Cloud Storage library
import cloudstorage as gcs
import logging
import webapp2

from google.appengine.ext import ndb

# our bucket name - will need to be edited before deploying our app
BUCKET_NAME = 'your_bucket_name'

# retry parameters should an upload fail
MY_DEFAULT_RETRY_PARAMS = gcs.RetryParams(initial_delay=0.2,
                                          max_delay=5.0,
                                          backoff_factor=2,
                                          max_retry_period=15)
gcs.set_default_retry_params(MY_DEFAULT_RETRY_PARAMS)


def guestbook_key(guestbook_name='default_guestbook'):
    return ndb.Key('Guestbook', guestbook_name)

jinja_environment = jinja2.Environment(
    loader=jinja2.FileSystemLoader(os.path.dirname(__file__)),
    extensions=['jinja2.ext.autoescape'],
    autoescape=True)


class Greeting(ndb.Model):
    content = ndb.StringProperty()
    date = ndb.DateTimeProperty(auto_now_add=True)


class MainPage(webapp2.RequestHandler):
    def get(self):
        greetings_query = Greeting.query(
            ancestor=guestbook_key()).order(-Greeting.date)
        greetings = greetings_query.fetch(10)
        template = jinja_environment.get_template('index.html')
        self.response.out.write(template.render(entries=greetings))

    def post(self):
        # add the entry to Datastore
        greeting = Greeting(parent=guestbook_key())
        greeting.content = self.request.get('entry')
        greeting.put()
        self.response.out.write(template.render(entries=greetings))
        
        # create a file from the entry
        filename = '/' + BUCKET_NAME + '/' + str(greeting.date)
        content = str(greeting.content)
        
        try:
            self.create_file(filename, content)
        except Exception, error:
            logging.exception(error)
            self.delete_file(filename)
        else:
            logging.info('The object was created in Google Cloud Storage')
        self.redirect('/')
        
    def create_file(self, filename, content):
        write_retry_params = gcs.RetryParams(backoff_factor=1.1)
        gcs_file = gcs.open(filename,
                            'w',
                            content_type='text/plain',
                            retry_params=write_retry_params)
        gcs_file.write(content)
        gcs_file.close()
        
    def delete_file(self, filename):
        try:
            gcs.delete(filename)
        except gcs.NotFoundError:
            pass
        logging.info('There was an error writing to Google Cloud Storage')

class Clear(webapp2.RequestHandler):
    def post(self):
        greetings_query = Greeting.query(
            ancestor=guestbook_key()).fetch(keys_only=True)
        ndb.delete_multi(greetings_query)
        self.redirect('/')

application = webapp2.WSGIApplication([
    ('/', MainPage),
    ('/clear', Clear),
], debug=True)
```

## Deploy our app

1. rename the bucket name inside the `guestbook.py` to your bucket's name
2. remove `application` and `version` fields from `app.yaml`
3. deploy the app

    ```bash
    $ gcloud app deploy app.yaml --project=<project-name> --version=<version>
    ```
4. deploy Datastore if it's no longer available from the Datastore demo:

    ```bash
    $ gcloud app deploy index.yaml
    ```
5. visiting the application will show the previous entries in Datastore if not deleted

## Inspect bucket

1. create a few entries via the web app
2. visit [Storage](https://console.cloud.google.com/storage/browser) and select your bucket
3. you'll see your entries. Clicking on an entry opens the file in the browser
4. files can be managed in bulk or individually
5. make a file publicly available by:
    1. selecting the menu on the right of the item
    2. edit permissions
    3. add an item of `User` with `allUsers` as the value and `Reader` as the access
    3. save
6. you'll see a `public link` column for the item that provides the URL

## Cleanup

Stop your compute instance
