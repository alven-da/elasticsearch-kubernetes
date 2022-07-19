# Elasticsearch Setup in Kubernetes (using minikube)

Note: This guide is for MacOS only

## Pre-requisistes

* Install hyperkit and minikube
  - `brew install hyperkit`
  - `brew install minikube`

* Install docker and docker-compose
  - `brew install docker`
  - `brew install docker-compose`

* Start minikube with 4 CPUs and (at least) 8GB of RAM: `minikube start --cpus 4 --memory 8192`

* After minikube created a VM, execute: `eval $(minikube docker-env)`

* Write minikube's IP into the host file (it may prompt for your password): ```echo "`minikube ip` docker.local" | sudo tee -a /etc/hosts > /dev/null```

* Open a separate terminal window and then execute: `minikube tunnel`

* Test Kubernetes CLI: `kubectl version`

## How to create an Elasticsearch cluster (v8.3.2)

Create the namespace named `infra` first: `kubectl create ns infra`

## There are 3 sets of YAML files to create an Elasticsearch pod

### Master

```
kubectl apply -f 00-elasticsearch-master-configmap.yaml \
-f 00-elasticsearch-master-service.yaml \
-f 00-elasticsearch-master-deployment.yaml
```

### Data

```
kubectl apply -f 00-elasticsearch-data-configmap.yaml \
-f 00-elasticsearch-data-service.yaml \
-f 00-elasticsearch-data-pvc.yaml \
-f 00-elasticsearch-data-statefulset.yaml \
-f 00-elasticsearch-data-storageclass.yaml 
```


### Client/Ingest

```
kubectl apply -f 00-elasticsearch-client-configmap.yaml \
-f 00-elasticsearch-client-service.yaml \
-f 00-elasticsearch-client-deployment.yaml
```

Apparently, client node needs a NodePort for it to be accessed externally:

```
kubectl apply -f 00-elasticsearch-client-nodeport.yaml
```

Then execute `minikube service elasticsearch-client-nodeport -n infra` to load the elasticsearch API