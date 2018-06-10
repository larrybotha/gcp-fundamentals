# 7. Configure Cloud SDK on A Compute Engine Instance

Cloud Shell is a comman line built into Google Developer Console, and is free.

## Creating a Compute Engine instance

1. In Cloud Console open [Compute Engine](https://console.cloud.google.com/compute)
2. Select 'Create'
    - give the instance a name
    - select the region closest to you
    - choose 'small' machine
    - choose latest Debian boot disk
        - choose 20GB size
    - create
3. Once created we can SSH in

## SSH into Compute Engine instance and install Cloud SDK

1. click 'SSH' on instance
2. update the local repo

    ```bash
    # update in quiet mode
    $ sudo apt-get -qq update
    ```
3. install Google Cloud SDK [as per their docs](https://cloud.google.com/sdk/docs/quickstart-debian-ubuntu)

    ```bash
    # Create environment variable for correct distribution
    export CLOUD_SDK_REPO="cloud-sdk-$(lsb_release -c -s)"

    # Add the Cloud SDK distribution URI as a package source
    echo "deb http://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list

    # Import the Google Cloud Platform public key
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

    # Update the package list and install the Cloud SDK
    sudo apt-get update && sudo apt-get install google-cloud-sdk
    ```

## Configure Cloud SDK

1. view the current properties for the SDK

    ```bash
    $ gcloud config list
    
    [core]
    account = [some-compute-id]@developer.gserviceaccount.com
    disable_usage_reporting = True
    project = [current-project-name]
    Your active configuration is: [default]
    ```
2. By default a service account will be used which is recommended for shared virtual environments. Let's switch this to a personal account:
    1. request auth
        ```bash
        # log in to gcloud, and return the url via stdout instead of opening a browser
        $ gcloud auth login --no-launch-browser
        ```
    2. visit the URL that the auth command outputs and copy the verification code
    3. enter the verification code into the prompt in the terminal
    4. now logged in with your account
3. you can verify the active account

    ```bash
    $ gcloud config list
    ```
4. configure defaults. Configuring default regions and zones saves a lot of typing as many commands require a specified region and zone. Defaults allow flags to be inserted automatically for you.

    1. view available regions
    
        ```bash
        $ gcloud compute regions list
        NAME                     CPUS  DISKS_GB  ADDRESSES  RESERVED_ADDRESSES  STATUS  TURNDOWN_DATE
        asia-east1               0/24  0/4096    0/8        0/8                 UP
        asia-northeast1          0/24  0/4096    0/8        0/8                 UP
        asia-south1              0/24  0/4096    0/8        0/8                 UP
        ...
        ```
    2. set the region for this instance
      
        ```bash
        $ gcloud config set compute/region europe-west1
        Updated property [compute/region].
        ```
    3. configure the zone. Zones always contain the region's name
    
    
        ```bash
        $ gcloud config set compute/zone europe-west1-b
        ```
    4. verify the config has been updated for this instance
    
        ```bash
        $ gcloud config list
        [compute]
        region = europe-west1
        zone = europe-west1-b
        [core]
        ...
        ```
    5. view details about the current instance
    
        ```bash
        $ gcloud compute instances list
        NAME        ZONE            MACHINE_TYPE  PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
        [name]      europe-west1-b  g1-small                   10.132.0.2   35.205.175.139  RUNNING
        ```

This is the long way of configuring an instance. Running `gcloud init` will run through everything above:

- authenticate you
- ask for defaults:
    - project
    - region
    - zone
    
Installation of the SDK is optional, as Google Cloud Shell can be opened via the developer console.
 
### Extras

- the current project can be set using `config set`

    ```bash
    $ gcloud config set project PROJECT_ID
    ```
- to see all authenticated accounts for the current project use `auth list`

    ```bash
    $ gcloud auth list
    ```

## Shutting down instance

*NB*: shut down the instance once done, otherwise you'll be charged for uptime.
