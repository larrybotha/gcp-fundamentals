# 12. Creating a cluster using the web UI

We'll build a cluster with:

- 1 replication controller
- 1 service
- 1 pod

## Create a cluster

1. visit [Kubernetes Engine](https://console.cloud.google.com/kubernetes/list)
2. click 'Create cluster'
3. Provide details:
    - name: my-cluster
    - machine type: n1-standard1
        - machine type is selected only once. All instances in the cluster use the same machine type.
    - cluster size: 1
4. The equivalent command line can be viewed from the link below 'Create'
5. click 'Create'
6. Start the Compute Instance created at #10

## Configure Kubectl

1. Open Cloud Shell
2. SSH into the compute instance

    ```bash
    $ gcloud compute ssh <instance-name>
    ```
3. clone the container configuration

    ```bash
    $ git clone https://github.com/GoogleCloudPlatformTraining/cp100-container-engine-python/
    ```
4. We can see defaults that we have set for the instance:
    
    ```bash
    $ gcloud config list
    [compute]
    region = europe-west1
    zone = europe-west1-b
    [core]
    account = <account-emai>
    disable_usage_reporting = True
    project = <project-name>
    ```
5. For convenience we'll set a default cluster

    ```bash
    $ gcloud config set container/cluster my-cluster
    ```
6. To get credentials for a cluster created in the web UI we need to do the following:

   ```bash
   $ gcloud container clusters get-credentials my-cluster
   ```
   
   If you cluster is in a different region to the default set, you'll need to define the region explicitly:
   
   ```bash
   $ gcloud container clusters get-credentials my-cluster --region=<cluster-region>
   ```
   
   We'll also need `kubectl` so we can run deployments etc. for our Kubernetes configs:
   
   ```bash
   $ sudo apt-get install kubectl
   ```
   
   This entire step can be eliminated when creating a cluster via the SDK.


## Create our replication controller

Instead of running a pod for each separate concern (frontend, database) we can create a single pod running multiple containers. That's what we'll do here.

1. inspect the guestbook replication controller config:

    ```bash
    $ cd cp100-container-engine-pyhon
    $ cat guestbook.yaml
    ```
   
    We have a configuration as follows:
       
    ```yaml
    apiVersion: v1
    # we are defining a replication controller
    kind: ReplicationController
    metadata:
      # this is the name of the replication controller
      name: guestbook
      # these are the labels associated with this replication controller
      labels:
        name: guestbook
    # the spec defines what this replication does
    spec:
      # Ensure there is always 1 replica / pod running at all times
      # This is the value can be used to scale the number of pods up or down
      replicas: 1
            image: redis
      # This replication controller is going to target pods that have the 
      # labels defined here
      selector:
        name: guestbook
      template:
        # we define our pod here, and give it the same label as defined in
        # the selector above
        metadata:
          labels:
            name: guestbook
        # this spec defines the containers that live inside a pod
        spec:
          containers:
          # this is our application frontend
          - name: python-guestbook
            # the docker image we'll pull from the docker registry
            image: google/guestbook-python-redis
            ports:
            - containerPort: 80
          # this is our application database
          - name: redis
            image: redis
            ports:
            - containerPort: 6379
    ```
2. create the replication controller

    ```bash
    4 kubectl create -f guestbook.yaml
    ```
3. we can now see the pods running

    ```bash
    $ kubectl get pods
    NAME              READY     STATUS              RESTARTS   AGE
    guestbook-gt2c8   0/2       ContainerCreating   0          10s
    #       [1]       [2]
    # 1 - the name of our pod
    # 2 - the number of containers running in this pod
    ```

## Make the frontend publicly available

We need a service that can route external traffic through to our frontend. We can use a `yaml` config as before to create a service, but it can also be done via the command line:

1. create the service

    ```bash
    # create a service exposing our guestbook replication controller on port 80 as a LoadBalancer
    $ kubectl expose rc guestbook --port=80 --type="LoadBalancer"
    service "guestbook" exposed
    ```
2. view running services

    ```bash
    $ kubectl get services
    NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
    guestbook    LoadBalancer   10.31.246.75   <pending>     80:32020/TCP   50s
    kubernetes   ClusterIP      10.31.240.1    <none>        443/TCP        1h
    ```
3. once we have an external IP on the LoadBalancer we can access our application:

    ```bash
    $ kubectl get services
    NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
    guestbook    LoadBalancer   10.31.246.75   35.204.143.88   80:32020/TCP   1m
    kubernetes   ClusterIP      10.31.240.1    <none>          443/TCP        1h
    ```
    
## Log onto container instance

1. visit [Compute Engine](https://console.cloud.google.com/compute/instances)
2. click 'SSH' on the cluster instance
3. view the docker processes:

    ```bash
    $ sudo docker ps
    CONTAINER ID        IMAGE                               COMMAND                  CREATED             
    STATUS              PORTS               NAMES
    a132f30cfd18        google/guestbook-python-redis@...   "python /app/app.py"     10 minutes ago      
    Up 10 minutes                           k8s_python-guestbook_guestbook-gt2...
    11e031276fe3        redis@sha256:6213e498c76eb159d...   "docker-entrypoint..."   10 minutes ago      
    Up 10 minutes                           k8s_redis_guestbook-gt2c8_default_...
    ...
    ```
4. view logs for our frontend container

    ```bash
    $ sudo docker logs a132f30cfd18
    * Running on http://0.0.0.0:80/
    10.28.2.1 - - [27/Jun/2018 09:04:26] "GET /robots.txt HTTP/1.1" 404 -
    10.28.2.1 - - [27/Jun/2018 09:04:26] "GET / HTTP/1.1" 200 -
    10.28.2.1 - - [27/Jun/2018 09:04:28] "GET /favicon.ico HTTP/1.1" 404 -
    10.28.2.1 - - [27/Jun/2018 09:04:32] "POST / HTTP/1.1" 302 -
    10.28.2.1 - - [27/Jun/2018 09:04:32] "GET / HTTP/1.1" 200 -
    ```

## Cleanup

1. visit [Kubernetes Engine](https://console.cloud.google.com/kubernetes/list)
2. select cluster, and click delete
3. visit [Network Services](https://console.cloud.google.com/net-services/)
4. we can see our LoadBalancer which forwards traffic on the IP address we accessed our frontend on to a target pool of virtual machines, i.e. the node that was inside our cluster
5. select and delete the LoadBalancer
6. visit [Compute Engine](https://console.cloud.google.com/compute)
7. stop the compute engine instance
