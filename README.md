# How to reproduce

1- run minikube, create a new namespace.  
2- apply these configs in order:  
`echo/deployment.yaml`  
`echo/service.yaml`  
`traefik/static.yaml`  
`traefik/dynamic.yaml`  
`traefik/deployment.yaml`  
`traefik/service.yaml`  
`loadtest/deployment.yaml`  

3- check the (6) pods are all running  
4- go inside loadtest pod:  
`kubectl exec -it <loadtest-pod> /bin/bash`  

5- there's a `run.sh` script there. it contains a loadtest sample with `ghz.sh` tool, which requests a thousand of gRPC calls that goes through traefik to call echo service. it uses multiple connections and concurrency.  

6- check prometheus metrics in each of `echo` pods:  
`kubectl port-forward <each-echo-pod> 5000:50050`  
`kubectl port-forward <each-echo-pod> 5001:50050`  
`kubectl port-forward <each-echo-pod> 5002:50050`  
now go to `localhost:5000` to `5002` and check `request_total`.  
You'll see that all requests were made to one pod!  
You can try and execute `run.sh` multiple times but the result stays the same!  