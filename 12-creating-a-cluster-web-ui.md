# 12. Creating a cluster using the web UI

We'll build a cluster with:

- 1 replication controller
- 1 service
- 1 pod

## Create a cluster

1. visit [Kubernetes Engine](https://console.cloud.google.com/kubernetes/list)
2. click 'Create cluster'
3. Provide details:
    - name
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
