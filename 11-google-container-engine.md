# 11. Google Container Engine

- containers give developers power over their environments - no longer have to worry about the host the application is running on.
- containers bring development and operations together
- without containers applications are generally deployed as monoliths
    - with containers we can separate applications into their own services, with their own teams, and own deployments
    
## Kubernetes

- open-source platform for orchestrating containers across clusters of machines
    - containers are great on their own, but orchestration is what allows one to:
        - schedule containers onto nodes
        - scale up/down
        - roll out udpates
        - balance containers across machines
- based on 10+ years of experience at Google
    - simplfies the following:
        - deployment
        - scaling
        - rolling out new features
        - load-balancing
- lean
    - lightweight
    - simple
    - accessible
- portable
    - public
    - private
    - hybrid
    - multi-cloud
- extensible
    - modular
    - pluggable
    - composable
- self-healing
    - auto-placement
    - auto-replication

## Google Container Engine (GKE)

[Container Engine](https://cloud.google.com/kubernetes-engine/)

- based on Kubernetes
    - containers as a service
        - Google Compute Engine
        - Docker
        - Kubernetes
- orchestrate and schedule docker containers
- consumes Compute Engine instances and resources
- uses a declarative syntax (using yml) to manage applications
- decouple operational and development concerns
- manages and maintains
    - logging
    - health management
    - monitoring
    - scaling
