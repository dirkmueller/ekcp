# [![Build Status](https://travis-ci.org/mudler/ekcp.svg?branch=master)](https://travis-ci.org/mudler/ekcp) Ekcp (Ephemeral Kubernetes Clusters Provider)

EKCP aims to build a simple API to provide Kubernetes environment for development and :rocket: lab environments.

# Requires

- Docker and docker-compose on the host

## Deploy with docker-compose

    $> git clone https://github.com/mudler/ekcp
    $> cd ekcp
    $> vim docker-compose.yml # Edit DOMAIN (pick one, reccomend to xip.io or nip.io) and KUBEHOST (your external IP)
    $> docker-compose up -d

## Simple API to create ephemeral clusters

### Create a new cluster

    curl -d "name=test" -X POST http://127.0.0.1:8030/new

### Delete a cluster

    curl -X DELETE http://127.0.0.1:8030/test

### Get a cluster kubeconfig file

    curl  http://127.0.0.1:8030/kubeconfig/test

### Store a kubeconfig from a local file

    curl -d "name=test&kubeconfig=$(base64 kubeconfig)" -X POST http://127.0.0.1:8030/api/v1/cluster/insert
    
### List available clusters:

    curl http://127.0.0.1:8030/ | jq -r .AvailableClusters

## Architecture

EKCP currently uses ```kind``` as backend to create new Kubernetes cluster. A proxy is setted up for each cluster to allow remote connection leveraging ```kubectl proxy```. Gorouter is setted up with docker-compose and the routes are registered to a NATS server if ```ROUTE_REGISTER=true``` is set, allowing to use the gorouter as http proxy to resolve internal domains.

## Openstack template

Openstack template is available [here](https://github.com/mudler/ekcp/tree/master/hack/openstack_heat).

It can be used to spawn a group of VM that will be automatically attached to an api master node (which has to be provided already, in the same network)

To recreate the stack, just point the template URL to `https://raw.githubusercontent.com/mudler/ekcp/master/hack/openstack_heat/template.yaml`.
