



kubectl get secret production-es-elastic-user -o go-template='{{.data.elastic | base64decode }}'

# Edit to add the newly generated password
kubectl create -f elastic-prod-daemonset-filebeat.yaml
kubectl delete -f elastic-prod-daemonset-filebeat.yaml


kubectl create -f log-generation.yml
kubectl delete -f log-generation.yml
