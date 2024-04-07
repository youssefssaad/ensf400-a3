# STEP 1
The nginx-dep.yaml file is responsible for defining the deployment. This deployment has 5 replicas which means 5 identical copies of the pod. Kubernetes ensures that the specified number of Pods are always running and available. The reason we use replicas is that it can ensure that the application remains available regardless if a pod crashes. More specifically, for this assignment they serve the purpose of load balancing. Incoming traffic is distributed across the several pods. Lastly, they conveniently help with scaling as you can easily create more or less pods based on need. For this step, we use the nginx image which is premade for us instead of making the whole image from scratch. We are expecting action on port 80.

# STEP 2
A configmap stores data in key-value pairs. This allows you to reconfigure the application at will without the need to rebuild/redeploy. Configmaps can be used for many cases such as storing external configuration data, setting environment variables, or even storing config files. Here we defined the configuration of the default.conf and the config file instead of having to work directly within the image. 

<<<<<<< HEAD
You will use Minikube in Codespaces to deploy an nginx service and 2 backend apps.
=======
# STEP 3
This step is just implementing the configmap as mentioned in step 2. For our deployment, we mount this configmap as a volume in the nginx-dep.yaml file. This makes our setup more modular allowing us to make big changes without having to rewrite many files.
>>>>>>> 4174dd3 (the curl command hangs)

# STEP 4
The purpose of a service file allows you to access the pods without needing to know their IP adresses. Instead, a service endpoint is used to access the set of pods that make up our nginx application here. As with the purpose of our work, we aim to have many instances running across various nodes. This allows for high availability and load balancing as mentioned above. Furthermore, we use ClusterIP. This is the default config type. ClusterIP makes the service only reachable from within an internal cluster on a cluster-internal IP. Lastly, the label selectors ensure that we use the correct pods from our nginx-dep.yaml file.

# STEP 5
An Ingress is a resource designed to allocate external access to the services within a cluster. For our case here, we use HTTP traffic. It is a user defined routing layer to direct the cluster's services. Through ingress, we can expose multiple services under a single IP address. This helps in loadbalancing ofcourse because it allows us to route to different backend services based on the request host or path. Traffic will be distributed efficiently. Through path-based routing, we can route to different backend services based on the URL path. This is done in our case as redirecting the requests to path / to the backend service nginx-svc. Also, we use nginx as the ingress controller to configure the routing. 

<<<<<<< HEAD
1. A `Deployment` config defined in `nginx-dep.yaml`. The Deployment has the name `nginx-dep` with 5 replicas. The Deployment uses a base image `nginx` with the version tag `1.14.2`. Expose port `80`.
1. A `ConfigMap` defined in `nginx-configmap.yaml`, The `data` in the configmap has a key-value pair with the key being `default.conf` and value being the following:
```
upstream backend {
    server app-1:8080;
    server app-2:8080;
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```
1. In the Deployment `nginx-dep`, mount the configuration file `default.conf` to the correct path of `/etc/nginx/conf.d` so that it serves as a load balancer, similar to what we have for [Assignment 2](https://github.com/denoslab/ensf400-lab5-ansible/tree/main/assignment2).
1. A `Service` config of type `ClusterIP` defined in `nginx-svc.yaml`. The service has the name `nginx-svc`, exposes port `80`, and should use label selectors to select the pods from the `Deployment` defined in the last step.
1. An `Ingress` config named `nginx-ingress.yaml` redirecting the requests to path `/` to the backend service `nginx-svc`. Example request and response:
```bash
$ curl http://$(minikube ip)/
Hello World from [app-1-dep-86f67f4f87-2d28z]!
$ curl http://$(minikube ip)/
Hello World from [app-2-dep-7f686c4d8d-lr95c]!
```
1. Write `Deployment` and `Service` for `app-1` and `app-2`, respectively.
1. Define two other `Ingress` configs named `app-1-ingress.yaml` and `app-2-ingress.yaml`, both redicting requests to `/` to the backend apps, taking `app-1` as the main deployment, and `app-2` as a canary deployment. The ingresses will redirect 70% of the traffic to `app-1` and 30% of the traffic to `app-2`. The docker images are pre-built for you. They can be downloaded using the URL below:
```
app-1: ghcr.io/denoslab/ensf400-sample-app:v1
app-2: ghcr.io/denoslab/ensf400-sample-app:v2
```
=======

Output: 
>>>>>>> 4174dd3 (the curl command hangs)


<<<<<<< HEAD
Submit the files below in a zip file. There is no need for TAs to access your Codespaces. TAs will mark your assignment based on the files you submitted.

1. (10%) `nginx-dep.yaml`
1. (10%) `nginx-configmap.yaml`
1. (10%) `nginx-svc.yaml`
1. (20%) `nginx-ingress.yaml`. Include steps showing the requests using `curl` and responses from load-balanced app backends (`app-1` and `app-2`).
1. (15%) `app-1-dep.yaml`, `app-1-svc.yaml`, `app-2-dep.yaml`, `app-2-svc.yaml`.
1. (20%) `app-1-ingress.yaml` and `app-2-ingress.yaml`.
1. (15%) A `README.md` Markdown file describing the steps and outputs meeting the requirements.
=======



# STEP 6
For this step, it is just a repeat of the file setup for nginx but this is for each app specifically. It is important we define dep and svc for each app independently because each one defines how traffic is routed and how the application is managed in an app specific way. Also, when it comes to scaling, each scale can be identified in a specific manner. As a result, we are able to benefit from the effects of traffic routing, load balancing which is done through distributing traffic across pods, and a sense of abstraction when it comes to covering the details of operation. All done in a specific manner adapted for each app.

# STEP 7
This step is also an implementation of the ingress file from before but done in an app specific way. Through this app speciffic ingress file, we are able to control the rollout thorugh directing incoming traffic. We use this through our canary deployment strategy. App-2 is our canary deployment which means testing the updates will be done through it while app-1 serves as the stable version. This strategy is very interesting as it allows us to make and test app changes without affecting the entire system deployment. This is crucial in the case that the test is flawed or crashes, as you probably do not want every user to be affected. The way it works is that 30% of the user traffic is routed to app-2 which has the new updates and meant to be a test to see how users react. The ingress routing works normally with the addition of the traffic splitting.

Output:






# Debugging
As I configured the assignment, I ran into some issues. These commands helped me figure out the issues. Firstly `kubectl get endpoints` helped me make sure that my services had endpoints and were listening on the correct port. From this command, I realized one of the errors I had was my ports were not aligned. `kubectl logs ...` would give me the log information so that I can try to debug the code. It would sometimes say that my file is written incorrectly or something isn't matched up. `kubectl get pods` helped me check if the pods were actually running and ready as they should be. If I noticed an issue here, it would mean I should look through my dep files and ensure things are set-up smoothly. 


# Commands
kubectl delete pods --all
kubectl delete deployments --all
kubectl delete svc --all
kubectl delete ingress --all
kubectl delete configmap --all
kubectl apply -f nginx-dep.yaml
kubectl apply -f nginx-configmap.yaml
kubectl apply -f nginx-svc.yaml
kubectl apply -f app-1-dep.yaml
kubectl apply -f app-1-svc.yaml
kubectl apply -f app-2-dep.yaml
kubectl apply -f app-2-svc.yaml
kubectl apply -f nginx-ingress.yaml
kubectl apply -f app-1-ingress.yaml
kubectl apply -f app-2-ingress.yaml
kubectl get deployments
kubectl get svc
kubectl get ingress
kubectl get pods
curl http://$(minikube ip)/
>>>>>>> 4174dd3 (the curl command hangs)
