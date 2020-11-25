# Kubernetes
kops:
----
```
kops create cluster   -> create a cluster configuration
kops update cluster   -> should update a cluster once its created to provision it
kops validate cluster -> check the cluster health
kops delete cluster   -> delete a cluster configuration
```
kubectl:
-------
```
kubectl cluster-info                                                 -> shows details and health stauts of cluster
kubectl get                                                          -> list the resources
kubectl describe                                                     -> show detailed information about a resource
kubectl logs                                                         -> print the logs from a container in a pod
kubectl exec                                                         -> execute a command on a container in a pod (like docker exec)
kubectl create                                                       -> creates a resource
kubectl apply                                                        -> does incremental changes to the resource
kubectl create -f <file.yaml>                                        -> creates a resource based on the config file specified
kubectl apply -f <file.yaml>                                         -> applies from the config file
kubectl delete                                                       -> deletes a resource
kubectl delete -f <file.yaml>                                        -> deletes a resource created using the config file
kubectl expose deployment <deployment> --port=<number> --type=<type> -> exposes deployment (types=NodePort, ClusterIP, LoadBalancer, ExternalName)
kubectl proxy                                                        -> sets up proxy to interact with the cluster
kubectl label                                                        -> labels a resource 
kubectl port-forward                                                 -> port forward to localhost
kubectl scale --replicas=<number>                                    -> scales a resource
kubectl set image deployments <deployment> <container>=<new image>   -> update images used

kubectl rollout status deployments <deployment>                      -> update rollout status
kubectl rollout undo deployments <deployment>                        -> undo rollback

kubectl wait --for=condition=<condition>                             -> waits until a certain condition is met

Go Templates: 
'{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}') -> extracts pod name
'{{range.spec.ports}}{{if .nodePort}}{{.nodePort}}{{"\n"}}{{end}}{{end}}') -> extract the port number assigned by Nodeport
```
