# 16 - Getting started with Cloud Datastore

We will deploy an App Engine application backed by Cloud Datastore by doing the following:

1. cloning hte project using our existing compute instance
2. deploy the app as we did in #14
3. view data

## Get access to the SDK

We will connect to our existing compute instance via Cloud Shell where we will clone and deploy our app:

- visit [Compute Engine](https://console.cloud.google.com/compute/instances)
- start your instance
- open Cloud Shell
- SSH into the instance

    ```bash
    $ gcloud compute ssh <instance-name> --zone=<instance-zone>
    ```


## Clone the project

- clone the app

    ```bash
    $ git clone https://github.com/GoogleCloudPlatformTraining/cp100-appengine-datastore-python.git
    $ cd cp100-appengine-datastore-python
    ```
- in this directory we have the same files as in #14, but with an additional `index.yaml`:

    ```bash
    $ ls
    app.yaml
    favicon.ico
    guestbook.py
    index.html
    index.yaml
    LICENSE
    README.md
    ```
- `index.yaml` contains index definitions for querying data from Datastore:

    ```yaml
    indexes:
    - kind: Greeting
      ancestor: yes
      properties:
      - name: date
        direction: desc
    ```
    
## Deploy the app

As in #14, our `app.yaml` contains configurations for deploying our app to App Engine:

```yaml
application: your-app-id
version: 1
runtime: python27
api_version: 1
threadsafe: true
handlers:
- name: jinja2
- url: /favicon\.ico
  static_files: favicon.ico
  upload: favicon\.ico

- url: /.*
  script: guestbook.application

libraries:
- name: webapp2
  version: latest
- name: jinja2
  version: latest
```

We need to remove `application` and `version` from `app.yaml`, and specify the project and version when deploying the app with the `gcloud app deploy` command.

- once the key-value pairs are removed, we can deploy our app:

    ```bash
    $ gcloud app deploy app.yaml --version 1-gurstbook-python-datastore
    ```
- visit [App Engine](https://console.cloud.google.com/appengine) to see the application instance running
- click the URL in the top-right of the logs
    - the app currently has a 500 error
    
        ```bash
        NeedIndexError: no matching index found. recommended index is:
        ```
    - we need to deploy our indexes
- to deploy the indexes:
    
    ```bash
    $ gcloud app deploy index.yaml
    ```
- visit [Datastore Indexes](https://console.cloud.google.com/datastore/indexes) to see that the indexes are being generated. The application indicates the same.
- once the indexes are created, you can visit the app

## View entries

- create a few entries on the app
- visit [Datastore Dashboard](https://console.cloud.google.com/datastore/stats)
    - select 'Greeting' from types. There is no data currently, but after 24 - 48 hours the dashboard will show useful stats for indexes
- visit [Datastore Entities](https://console.cloud.google.com/datastore/entities)
    - here one can see the entries previously added
    - we can query by type
    - we can filter by different options
    - we can query using QGL directly
- click any specific entity. We can see:
    - the entity is of kind 'Greeting'
    - key
        - this is hardcoded in `guestbook.py`:
        
            ```python            
            def guestbook_key(guestbook_name='default_guestbook'):
                return ndb.Key('Guestbook', guestbook_name)
            ```
        - all 'Greeting' entities are children of this key
        - this ensures that all entities belong to this entity group, and when a value is submitted on the application it's shown on refresh. If this isn't done, we would have *eventual consistency*, and entities may or may not be shown on refresh, depending on how busy Datastore is.
        - we have two indexed properties:
            - content
            - date
- under [Datastore Admin](https://console.cloud.google.com/datastore/settings) we can enable backing up, restoring, copying, and deleting

## Evaluating the code

### `index.yaml`

```yaml
# this is what configures our Datastore, and allows ndb in guestbook.py to query children
indexes:
# we have kind of Greeting
- kind: Greeting
  # that supports ancestor queries
  ancestor: yes
  properties:
  # on the property 'date'
  - name: date
    # with a direction of descending
    direction: desc
```


### `index.html`

```html
...
          <!-- post to index when submitting -->
          <form role="form" action="" method=post>
              <div class="form-group">
                <textarea class="form-control" name=entry rows="3"></textarea>
              </div>
              <button type="submit" class="btn btn-primary">Submit</button>
          </form>
...
      <ul class="list-group">
        {% for entry in entries %}
        <!-- output the `content` property of each entry -->
        <li class="list-group-item" class=entry>{{ entry.content }}</li>
        {% endfor %}
      </ul>
      
      <!-- post to /clear when clearing entries -->
      <form role=form action="/clear" method=post>
        <button type="submit" class="btn btn-danger">Clear Entries</button>
      </form>
...
```


### `guestbook.py`

```python
import jinja2
import os
import webapp2

# Use ndb from the App Engine client libraries to make working with Datastore and
# modeling entities in Python easy
from google.appengine.ext import ndb

# define our key here - this is what we use to tie relate our Greeting child entities
# to our parent Guestbook entity
def guestbook_key(guestbook_name='default_guestbook'):
    return ndb.Key('Guestbook', guestbook_name)

jinja_environment = jinja2.Environment(
    loader=jinja2.FileSystemLoader(os.path.dirname(__file__)),
    extensions=['jinja2.ext.autoescape'],
    autoescape=True)


# the definition for our Greeting model
class Greeting(ndb.Model):
    content = ndb.StringProperty()
    date = ndb.DateTimeProperty(auto_now_add=True)


# requests for our app - essentially the same as a route handler in Express
class MainPage(webapp2.RequestHandler):
    # GET requests
    def get(self):
        # create the query to fetch greetings
        # i.e.
        # perform an ancestor query,
        # getting all the children under guestbook_key,
        # and order them descending
        greetings_query = Greeting.query(
            ancestor=guestbook_key()).order(-Greeting.date)
        # get our greetings - the first 10
        greetings = greetings_query.fetch(10)
        # get the template we will populate
        template = jinja_environment.get_template('index.html')
        # send a response using the template above, rendering greetings
        # as the entries value in the template
        self.response.out.write(template.render(entries=greetings))

    # POST requests
    def post(self):
        # specify the entity we will be creating
        # This is where we are setting up strong consistency so that this entity
        # has a parent of the specified key
        greeting = Greeting(parent=guestbook_key())
        # set the content of the entity to the `entry` key of the request
        greeting.content = self.request.get('entry')
        # write the entity to Datastore
        greeting.put()
        # redirect the user to the index page
        self.redirect('/')


# /clear route handler
class Clear(webapp2.RequestHandler):
    # only accepts POST requests. We get a 405 attempting to GET /clear
    def post(self):
        # configure our query
        # fetch only the keys, as that's all we need in order to delete
        # entries. This is more performant than fetching all the data
        # for each entry
        greetings_query = Greeting.query(
            ancestor=guestbook_key()).fetch(keys_only=True)
        # delete entries by passing in the keys for the greetings
        ndb.delete_multi(greetings_query)
        # redirect to index once complete
        self.redirect('/')
        
# map our routes to our route handlers
application = webapp2.WSGIApplication([
    ('/', MainPage),
    ('/clear', Clear),
], debug=True)
```
