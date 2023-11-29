### Reference links:
- [Istio getting started official guide](https://istio.io/latest/docs/setup/getting-started/)
- [Istio tutorial](https://medium.com/google-cloud/istio-service-mesh-101-part-1-3-f07a8fedeea8)


### Deploy Bookinfo sample application
```
# Deploy the Bookinfo sample application. 
# The application will start. As each pod becomes ready, the Istio sidecar will be deployed along with it.
kubectl apply -f ~/work/istio-1.20.0/samples/bookinfo/platform/kube/bookinfo.yaml

# List services:
kubectl get services

# List pods:
# Make sure there are two containers in every pod.kubectl get pods

# Check if the app is running inside the cluster and serving HTML pages by checking for the page title in the response:
kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
 # Sample output: <title>Simple Bookstore App</title>
```

### Open the application to outside traffic
The Bookinfo application is deployed but not accessible from the outside. To make it accessible, you need to create an Istio Ingress Gateway, which maps a path to a route at the edge of your mesh.

```
# Associate this application with the Istio gateway:

kubectl apply -f  ~/work/istio-1.20.0/samples/bookinfo/networking/bookinfo-gateway.yaml
 #gateway.networking.istio.io/bookinfo-gateway created
 #virtualservice.networking.istio.io/bookinfo created

# Ensure that there are no issues with the configuration:
istioctl analyze
```


### Determining the ingress IP and ports
set the INGRESS_HOST and INGRESS_PORT variables for accessing the gateway.

```
# Minikube - Run in a new terminal window
# start a Minikube tunnel that sends traffic to your Istio Ingress Gateway. 
# This will provide an external load balancer, EXTERNAL-IP, for service/istio-ingressgateway.

minikube tunnel

#Set the ingress host and ports variables:
export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].port}')

# Ensure an IP address and ports were successfully assigned to each environment variable:

echo "$INGRESS_HOST"        #127.0.0.1
echo "$INGRESS_PORT"        #80
echo "$SECURE_INGRESS_PORT" #443

# Set GATEWAY_URL:
export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT
echo "$GATEWAY_URL"         # 127.0.0.1:80

### Verify external access

# View the Bookinfo product page using a browser.   ex: http://10.108.229.139/productpage
echo "http://$GATEWAY_URL/productpage"      # Paste the output from the previous command into your
```

## Telemetry - View the dashboard

```
# Install Kiali and the other addons and wait for them to be deployed.

kubectl apply -f ~/work/istio-1.20.0/samples/addons
kubectl rollout status deployment/kiali -n istio-system

# send a some requests to the productpage service
for i in $(seq 1 100); do curl -s -o /dev/null "http://$GATEWAY_URL/productpage"; done
```

istioctl dashboard kiali
In the left navigation menu, select Graph and in the Namespace drop down, select default.


The Kiali dashboard shows an overview of mesh with the relationships between the services in the Bookinfo sample application. It also provides filters to visualize the traffic flow.



### Fetch the service and App information
kubectl get all -n istio-system

```
Output:
NAME                                       READY   STATUS    RESTARTS   AGE
pod/grafana-7bd5db55c4-7cvz7               1/1     Running   0          147m
pod/istio-egressgateway-587d8cdb96-7vp2v   1/1     Running   0          163m
pod/istio-ingressgateway-d44f5ccd6-p4zzc   1/1     Running   0          163m
pod/istiod-8d5c88bcc-f9jt9                 1/1     Running   0          163m
pod/jaeger-78756f7d48-qj5r2                1/1     Running   0          147m
pod/kiali-55bfd5c754-f7hdt                 1/1     Running   0          147m
pod/prometheus-67f6764db9-p5djh            2/2     Running   0          147m

NAME                           TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)                                                                      AGE
service/grafana                ClusterIP      10.102.106.0     <none>           3000/TCP                                                                     147m
service/istio-egressgateway    ClusterIP      10.102.242.47    <none>           80/TCP,443/TCP                                                               163m
service/istio-ingressgateway   LoadBalancer   10.108.229.139   10.108.229.139   15021:30189/TCP,80:31047/TCP,443:31514/TCP,31400:31198/TCP,15443:30421/TCP   163m
service/istiod                 ClusterIP      10.105.107.14    <none>           15010/TCP,15012/TCP,443/TCP,15014/TCP                                        163m
service/jaeger-collector       ClusterIP      10.103.171.90    <none>           14268/TCP,14250/TCP,9411/TCP,4317/TCP,4318/TCP                               147m
service/kiali                  ClusterIP      10.96.235.66     <none>           20001/TCP,9090/TCP                                                           147m
service/loki-headless          ClusterIP      None             <none>           3100/TCP                                                                     147m
service/prometheus             ClusterIP      10.96.208.132    <none>           9090/TCP                                                                     147m
service/tracing                ClusterIP      10.96.160.193    <none>           80/TCP,16685/TCP                                                             147m
service/zipkin                 ClusterIP      10.107.6.24      <none>           9411/TCP                                                                     147m

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           147m
deployment.apps/istio-egressgateway    1/1     1            1           163m
deployment.apps/istio-ingressgateway   1/1     1            1           163m
deployment.apps/istiod                 1/1     1            1           163m
deployment.apps/jaeger                 1/1     1            1           147m
deployment.apps/kiali                  1/1     1            1           147m
deployment.apps/prometheus             1/1     1            1           147m

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7bd5db55c4               1         1         1       147m
replicaset.apps/istio-egressgateway-587d8cdb96   1         1         1       163m
replicaset.apps/istio-ingressgateway-d44f5ccd6   1         1         1       163m
replicaset.apps/istiod-8d5c88bcc                 1         1         1       163m
replicaset.apps/jaeger-78756f7d48                1         1         1       147m
replicaset.apps/kiali-55bfd5c754                 1         1         1       147m
replicaset.apps/prometheus-67f6764db9            1         1         1       147m
```


kubectl get pods -ALL

```
Output:
NAMESPACE              NAME                                        READY   STATUS      RESTARTS      AGE    L
default                details-v1-7745b6fcf4-v8swp                 2/2     Running     0             163m   
default                loki-0                                      2/2     Running     0             147m   
default                productpage-v1-6f89b6c557-flj4h             2/2     Running     0             163m   
default                ratings-v1-77bdbf89bb-smxzg                 2/2     Running     0             163m   
default                reviews-v1-667b5cc65d-pdvbr                 2/2     Running     0             163m   
default                reviews-v2-6f76498fc8-q8kdd                 2/2     Running     0             163m   
default                reviews-v3-5d8667cc66-m42jf                 2/2     Running     0             163m   
ingress-nginx          ingress-nginx-admission-create-x9b4v        0/1     Completed   0             12h    
ingress-nginx          ingress-nginx-admission-patch-br52m         0/1     Completed   1             12h    
ingress-nginx          ingress-nginx-controller-77669ff58-94xkv    1/1     Running     2 (11h ago)   12h    
istio-system           grafana-7bd5db55c4-7cvz7                    1/1     Running     0             148m   
istio-system           istio-egressgateway-587d8cdb96-7vp2v        1/1     Running     0             164m   
istio-system           istio-ingressgateway-d44f5ccd6-p4zzc        1/1     Running     0             164m   
istio-system           istiod-8d5c88bcc-f9jt9                      1/1     Running     0             164m   
istio-system           jaeger-78756f7d48-qj5r2                     1/1     Running     0             148m   
istio-system           kiali-55bfd5c754-f7hdt                      1/1     Running     0             147m   
istio-system           prometheus-67f6764db9-p5djh                 2/2     Running     0             147m   
kube-system            coredns-787d4945fb-mb9xb                    1/1     Running     2 (11h ago)   12h    
kube-system            etcd-minikube                               1/1     Running     2 (11h ago)   12h    
kube-system            kube-apiserver-minikube                     1/1     Running     2 (11h ago)   12h    
kube-system            kube-controller-manager-minikube            1/1     Running     2 (11h ago)   12h    
kube-system            kube-proxy-fkdkj                            1/1     Running     2 (11h ago)   12h    
kube-system            kube-scheduler-minikube                     1/1     Running     2 (11h ago)   12h    
kube-system            metrics-server-5f8fcc9bb7-25ggv             1/1     Running     3 (11h ago)   12h    
kube-system            storage-provisioner                         1/1     Running     5 (11h ago)   12h    
kubernetes-dashboard   dashboard-metrics-scraper-5c6664855-rd7jj   1/1     Running     2 (11h ago)   12h    
kubernetes-dashboard   kubernetes-dashboard-55c4cbbc7c-vz9zn       1/1     Running     3 (11h ago)   12h 
```


### Cleanup
```
# Delete the Bookinfo sample application and its configuration
~/work/istio-1.20.0/samples/bookinfo/platform/kube/cleanup.sh

kubectl delete -f ~/work/istio-1.20.0/samples/addons

kubectl delete -f  ~/work/istio-1.20.0/samples/bookinfo/networking/bookinfo-gateway.yaml

kubectl delete -f ~/work/istio-1.20.0/samples/bookinfo/platform/kube/bookinfo.yaml

```
