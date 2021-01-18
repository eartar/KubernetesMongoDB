# KubernetesMongoDB

Me messing around with kubernetes for the first time.

This is for me as a cheat sheet when i get confused about things, otherwise has no value whatsoever :)

If you really, really want to see whats going on, see it in "Raw View" in github.

@Myself: Don't forget about https://github.com/eartar/KubernetesDocker

# Cheat sheet for myself

kubectl get services

kubectl create deployment nginx-depl --image=nginx

CRUD ops

kubectl edit deployment nginx-depl
if edited, the pod will be terminated
in this case the replicaset wont have any running pods
A new replicaset and pod will be created
kubectl get pod
kubectl get replicaset

kubectl describe pod mongo-depl
clean output of whats going on with the pod. Events etc

kubectl exec -it mongo-depl-5fd6b7d4b4-zjjkx -- bin/bash
interactive terminal of this pod

kubectl apply -f nginx-deployment.yaml
set deployment file, changed deployment doesnt kill the previous pod


in yaml
spec:selector:key:val
is matced with deployment yaml

kubectl get pod -o wide
to get ip adress of pods
can use
kubectl describe service mongodb-service
to compare if ports are correctly routed


kubectl get deployment nginx-deployment -o yaml
gets yaml output from etcd for deployment

secret must be created before deployment also configmap
secret has username and password in base64 encoding

kubectl apply -f .\mongodb-secret.yaml

kubectl get all | select-string mongodb
or grep for unix to filter for mongodb stuff

config map is centralized

kubectl get pod --watch
semi-realtime updates

type: LoadBalancer -> assigns service an external IP address so it can accept external requests
requires nodeport under ports, which is between 30k-32767


namespaces
kube-system
- system processes
- master and kubectl

kube-public
- configmap
kubectl cluster-info

kube-node-lease
- heartbeats of nodes
- availablility of node

default
- yeah, default.

kubectl create namespace asdasfksakd
or with namespace config file

why namespaces?
have namespaces like database, monitoring, elastic stack, nginx-ingress etc
multiple teams, so no overrides. also can give access rights
to seperate staging and development environments
blue(current)/green(next) deployment
can limit namespace resources cpu, ram, storage..

*you cant access most resources from another namespace
- configmap
- secret

can access
- service (mysql-service.database)
				svc			ns

kubectl api-resources --namespaced=false
kubectl api-resources --namespaced=true
some things are no namespaceable, always global
- vol
- node

kubectl get configmap
is same as
kubectl get configmap -n default


kubectl apply -f mysql-configmap.yaml --namespace=asjasd creates the file in the namespace
or use a configuration file metadata:namespace:sdkf

kubens helps you swap between namespaces


ingress
wo ingress; you create an external server port, but the final product shouldnt have ip port in link
so you use ingress.
keep service internal, let ingress route external req to internal service

spec:rules: the .com domain
spec:http:paths: /.. url

an ingress controller pod is needed
controller is;
- to evaluate rules & redirections
- is the entrypoint to cluster
- third party implementations (kubernetes' one is Nginx Ingress Controller)

cluster architecture
for aws/gcloud

one of the common strategies for aws/gcloud;
Cloud Provider's Load balancer should redirect to ingress controller
No need load balancer implementation yourself

w/o cloud provider;
have an external proxy server with public ip as an etrypoint to cluster through the ingress controller
k8s cluster should not be accessible from outside

minikube addons enable ingress
