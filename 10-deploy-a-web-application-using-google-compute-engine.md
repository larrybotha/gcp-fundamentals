# 10. Deploy a web application using Google Compute Engine

## Steps

1. create a Compute Engine instance
2. deploy a sample 'Guestbook' application
3. expose the server to the internet by modifying the firewall
4. test the connection
5. delete the instance and associated disk

## Create a Compute Engine instance

An instance can be created via Cloud SDK or via the web UI. We'll use the web UI.

1. Ensure you are on the correct project
2. Visit [Compute Engine](https://console.cloud.google.com/compute/instances)
3. click 'Create Instance'
4. select the closest region to you
5. select 'small' machine type
6. define a boot disk
    - this will create a root-persistent disk that contains a boot loader, OS, and file system from the image we select for the disk
    - select CoreOs stable
    - leave boot disk type as 'standard' SSD is also an option
    - default disk size is suitable for this demo
7. we now have the minimum requirements for an instance:
    - name
    - zone
    - machine type
    - boot disk
8. we can view the command line equivalent of the above settings using the 'command line' link at the bottom of the page:

    ```bash
    gcloud beta compute --project=r<project-id> instances create <instance-name> \
        --zone=europe-west2-a \
        --machine-type=g1-small \
        --subnet=default \
        --network-tier=PREMIUM \
        --maintenance-policy=MIGRATE \
        --service-account=<service-account> \
        --scopes=<scopes> \
        --image=coreos-stable-1745-6-0-v20180611 \
        --image-project=coreos-cloud \
        --boot-disk-size=9GB \
        --boot-disk-type=pd-standard \
        --boot-disk-device-name=<instance-name>
    ```
9. once the instance is created we can see it running, and SSH into it

## Deploy an application onto the instance

There are a few ways to deploy an application to an instance:

1. SSH through the browser window
2. view the `gcloud` command by using the menu next to the SSH button on the instance line item
    - command can be executed using a locally installed Cloud SDK or through the Cloud Shell
    
We'll SSH through the browser window:

1. click SSH on the instance line item
2. we need a backend to store data entered via the frontend
    - we'll use a redis backend
    - we'll use Docker to spin up the backend
3.
    ```bash
    # create a detached docker container that:
    # runs in detached mode
    # has a name of redis_db
    # and is built from the redis image on Dockerhub
    $ docker run -d --name redis_db redis
    ```
    
    Once the image is created we need to:
    
    1. download and build a container for the frontend
    2. link the backend and frontend containers
    3. expose the application to the internet
4. confirm the container is running:

    ```bash
    $ docker ps
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
    d441e0986893        redis               "docker-entrypoint.s…"   2 minutes ago       Up 2 minutes        6379/tcp            redis_db
    ```
5. download the application. `git` is already installed, so all we need to do is clone it:

    ```bash
    $ git clone https://github.com/larrybotha/cp100-compute-engine-python
    ```
    
    This repo contains a Docker file and the application, so we can Docker's `build` command to build an image.
6. build the application's Docker image:

    ```bash
    $ cd cp100-compute-engine-python
    
    # tag the build so that we can reference, and use the current directory as the context for building the image
    $ docker build -t app-frontend/python-redis .
    ```
    
    What this has done is biult an image with all the dependencies our application needs, such as Python runtime, redis etc.
7. run the container

    ```bash
    # run the container
    # exposing port 80 inside the container to port 80 outside the container
    # linking the redis_db container we created to the db namespace inside the container
    # and running the image called app-frontend/python-redis
    $ docker run -p 80:80 --link redis_db:db app-frontend/python-redis
    ```
8. attempting to visit the external IP (available via the isntance line item in Cloud Console) will timeout - we haven't yet allowed HTTP traffic to the instance
9. firewall rules can modified in 2 ways:
    - go to [Firewall rules](https://console.cloud.google.com/networking/firewalls) udner Network settings and add a rule there
    - edit the instance directly to allow HTTP traffic
10. on the instance's page:
    - click 'Edit'
    - check 'allow HTTP traffic'
    - click 'save'
11. once the firewall rule is configured, an HTTP rule is added to the [Firewall rules](https://console.cloud.google.com/networking/firewalls) page
    - on the firewall rules the HTTP rule has a `http-server` target
    - on the instance there's a *Network tag* with `http-server` signifying the relationship between the rule and the instance
    
## Tearing down the instance

- the instance is created with a boot disk. This can be automatically deleted when the instance is deleted by configuring settings on the instance itself. By default the boot disk is deleted when the instance is deleted.
- the disks associated with the instances in the current project can be viewed on the  [Disks](https://console.cloud.google.com/compute/disks) page
- the instance can be deleted either through the web UI or the the Cloud SDK
