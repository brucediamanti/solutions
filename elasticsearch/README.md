# Deploying Elasticsearch & Kibana on Diamanti

[Elasticsearch](https://www.elastic.co/) is a search engine based on Lucene. It provides a distributed, multitenant-capable full-text search engine with an HTTP web interface and schema-free JSON documents. Elasticsearch is developed in Java and is released as open source under the terms of the Apache License.

To use these files, you will need to install the [Elastic Cloud on Kubernetes (ECK)](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-quickstart.html). Simply follow the instructions to install the latest version of the operator.

## Introduction

In this document we will discuss how to deploy Elasticsearch and Kibana, with the optional Fluentbit logging component on the Diamanti platform. The resources provided assume that your cluster has _248GB_ of RAM available and at least _1.5TB_ of storage, these values are needed in order to support a production level deployment. You can modify them to fit your requirements.

<!-- TOC -->

- [Deploying Elasticsearch & Kibana on Diamanti](#deploying-elasticsearch--kibana-on-diamanti)
  - [Introduction](#introduction)
  - [Configuration Options](#configuration-options)
  - [Other Configuration Options](#other-configuration-options)
    - [Resources](#resources)
  - [Installing](#installing)
    - [Elastic Password Retrieval](#elastic-password-retrieval)
  - [Installing Kibana](#installing-kibana)
    - [Connecting to Kibana](#connecting-to-kibana)
    - [Other Post Install Steps](#other-post-install-steps)
  - [Undeploying](#undeploying)
  - [TODO put these in the README properly](#todo-put-these-in-the-readme-properly)

<!-- /TOC -->

## Configuration Options

You can choose to enable or disable the different tiers of Data nodes. The default configuration provides: *Hot*, *Warm* and *Cold* storage nodes, each respectively using the Diamanti built in `StorageClasses` of `high`, `medium` and `best-effort`.

## Other Configuration Options

The files have been peppered with comment fields around relevant blocks which you may want to modify in order to run this in a more customized way, e.g. changing the namespace it runs under.

To find these simply grep the files for `#D20#` and all relevant section will return.

### Resources

You can specify the resource limits for each node type in the `prod-ready-cluster.yaml` file. Here is an example:

```yaml
volumeClaimTemplates:
  - metadata:
      name: elasticsearch-data
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 256Gi
      storageClassName: high
containers:
  - name: elasticsearch
    resources:
      requests:
        memory: 16Gi
        cpu: 4
      limits:
        memory: 16Gi
        cpu: 4
```

## Installing

Simply execute `kubectl apply -f prod-ready-cluster.yaml`

### Elastic Password Retrieval

The password for the cluster is a randomized string which you can extract with the following:

```console
kubectl get secret production-es-elastic-user -o go-template='{{.data.elastic | base64decode }}'
```

## Installing Kibana

You can install Kibana alongside for UI access: `kubectl apply -f prod-kibana.yaml`

### Connecting to Kibana

You can visit the Kibana pod IP in the browser as such: _http://$IP:5601_ using username: `elastic` and password: `extracted from above`

### Other Post Install Steps

## Undeploying

To uninstall/delete simply:

```bash
kubectl delete -f prod-ready-cluster.yaml -f prod-kibana.yaml
```

This command removes all the Kubernetes components associated with the chart and deletes the release.

## TODO put these in the README properly

kubectl create -f elastic-prod-daemonset-filebeat.yaml
kubectl delete -f elastic-prod-daemonset-filebeat.yaml

kubectl create -f log-generation.yml
kubectl delete -f log-generation.yml
