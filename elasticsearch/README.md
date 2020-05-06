# Deploying Elasticsearch & Kibana on Diamanti

[Elasticsearch](https://www.elastic.co/) is a search engine based on Lucene. It provides a distributed, multitenant-capable full-text search engine with an HTTP web interface and schema-free JSON documents. Elasticsearch is developed in Java and is released as open source under the terms of the Apache License.

## Introduction

In this document we will discuss how to deploy Elasticsearch and Kibana, with the optional Fluentbit logging component on the Diamanti platform. The resources provided assume that your cluster has _248GB_ of RAM available and at least _1.5TB_ of storage, these values are needed in order to support a production level deployment. You can modify them to fit your requirements.

<!-- TOC -->

- [Deploying Elasticsearch & Kibana on Diamanti](#deploying-elasticsearch--kibana-on-diamanti)
  - [Introduction](#introduction)
  - [Configuration Options](#configuration-options)
    - [Resources](#resources)
    - [Other Configuration Options Here](#other-configuration-options-here)
  - [Installing the Chart](#installing-the-chart)
    - [Installed Components](#installed-components)
    - [SA Password Retrieval](#sa-password-retrieval)
  - [Post Install Steps](#post-install-steps)
    - [Connecting to ...](#connecting-to)
    - [Other Post Install Steps ...](#other-post-install-steps)
  - [Undeploying](#undeploying)
- [Edit to add the newly generated password](#edit-to-add-the-newly-generated-password)

<!-- /TOC -->

## Configuration Options

You can choose to enable or disable the different tiers of Data nodes. The default configuration provides: *Hot*, *Warm* and *Cold* storage nodes, each respectively using the Diamanti built in `StorageClasses` of `high`, `medium` and `best-effort`.

<!-- Other H3 Configuration Items are added in here. These can describe the above values as needed. Some examples given. -->

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

### Other Configuration Options Here

## Installing the Chart

You can install by specifying parameters using the `--set key=value[,key=value]` argument to `helm install`. Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart.

># NOTE: You can copy and customize the default [values.yaml](values.yaml)

For example, to install the chart using the default [values.yaml](values.yaml) file with the value name `value1` being set to `456`, run:

```bash
$ dctl login

$ helm repo add $someDiamantiURLTBD$

$ helm install --name my-release-name --namespace my-namespace \
    --set value1=456 -f values.yaml solutions/solution
```

After a few minutes, you should see <!-- description of what they see here -->

### Installed Components

You can use `kubectl get` to view all of the installed components. Sample output as follows:
<!-- some sample output if it's useful -->

### SA Password Retrieval
<!-- This is an example of another installed component that should be specifically mentioned -->
The sa password is a randomized in the secret.yaml file.  To retrieve the password, perform the following steps once you install the helm chart.

```console
$ printf $(kubectl get secret --namespace default mysecretname -o jsonpath="{.data.sapassword}" | base64 --decode);echo
```

## Post Install Steps

<!-- some example post install steps that might be useful or necessary for the customer -->

### Connecting to ...

### Other Post Install Steps ...

## Undeploying

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete --purge my-release
```

This command removes all the Kubernetes components associated with the chart and deletes the release.



kubectl get secret production-es-elastic-user -o go-template='{{.data.elastic | base64decode }}'

# Edit to add the newly generated password
kubectl create -f elastic-prod-daemonset-filebeat.yaml
kubectl delete -f elastic-prod-daemonset-filebeat.yaml


kubectl create -f log-generation.yml
kubectl delete -f log-generation.yml
