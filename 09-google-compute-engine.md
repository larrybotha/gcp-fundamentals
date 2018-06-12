# 9. Google Compute Engine

GCP's IaaS offering allowing one to spin up virtual machines and networks of virtual machines. Google manages the hardware layer, we are fully responsible for software layer.

- innovative pricing
    - sub-hour billing
        - paying by the minute with 10 minute minimum
        - discounted pricing for sustained usage
    - preemptible instances
        - up to 70% cheaper
        - short-lived - up to 24 hours before being terminated
- robust networking features
- instance metadata and startup scripts
- wide range of machine types
    - can specify custom machine attributes
- HTTP and network load balancing
- Google Cloud Monitoring
    - create uptime checks
    - get notified when there are issues
    - creat dashboards from generated metrics
- persistent disk snapshots
    - makes it easy to backup or migrate data
- APIs for auto-scaling and group management
    - group management allows for pools of virtual machines (instance groups) based on a template
    - auto-scaling allows for machines to be automatically scaled within an instance group based on specified metrics
    
## Compute Engine Resources

- instances provide CPU and memory
- standard, SSD, and local SSD persistent disks
    - standard and SSD are network attached
    - local SSD is directly attached storage used for intensive workloads
- images for Linux, UNIX, and Windows Server
- support for disk snapshots
- networks with firewall rules
    - networks allow one to create many virtual machines that communicate with each other
    - firewall rules dictate traffic coming in or out of the network
- forwarding rules for load balancing
    - balance traffic across the network

When we spin up a Compute Engine virtual machine instance we:

- define a region and zone for the instance
- define the physical specifications of the machine
- attach persistent disks to the machine
- attach a boot disk with an OS, bootloader, and file system

## Compute Engine in developer console

[Compute Engine](https://console.cloud.google.com/compute)

In the navigation:

- VM Instances
    - list of instances for the current project
- Instance groups
    - managing pools of VMs
- Instance templates
    - templates for spinning up instance groups
- Disks
    - persistent data attached to instances
    - disks can be created independently and then attached to instances
- Snapshots
    - backed up data
- Images
    - available OSs to configure VMs with
- Metadata
    - project-wide metadata
    - each instance has its own metadata, but project-wide metadata can be configured here that all instances have access to
- Health checks
    - used with load balancing
    
## Networking in developer console

[VPC Networks](https://console.cloud.google.com/networking/networks)

## Compute Engine Billing

- instances are charged for while running (and can be switched off)
- disks are charged for bt allocated amount (not amount in use)
- networks are charged for on certain operations
    - ingress (inbound traffic) _generally_ has no charge
    - egress (outbound traffic) it depends
        - same zone, generally no charge
        - between zones / regions charges vary depending on geographical location
- [pricing calculator](http://cloud.google.com/products/calculator)
- [Compute Engine pricing](https://cloud.google.com/compute/pricing) is region-specific

## CLI - gcloud compute

Cloud SDK includes `gcloud compute` for managing GCE resources, e.g.

```bash
# create a compute instance
$ gcloud compute instances create <instance_name> --zone <zone> --project <id>
#   [1]    [2]      [2]    [          3         ]
# 1 - gcloud SDK
# 2 - compute command
# 3 - command group / resource
# 4 - commands

# get all compute command groups / resources
$ gcloud compute -h

# get all operations that can be performed on a specific command group / resource
$ gcloud compute instances -h
```

## Accessing Instances

- access Linux and UNIX instances of SSH
    - access via Cloud SDK
    
        ```bash
        $ gcloud compute ssh <instance>
        ```
    - over standard SSH
    - from the developer console
- access Windows Server instances over Remote Desktop Protocol (RDP)
