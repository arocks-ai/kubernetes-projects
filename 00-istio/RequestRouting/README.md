## Istio Request Routing using Virtual Service
The projects shows following Traffic Management
- Route All the traffic to version 1 of the service 
- Route the traffic coming from the specific user to v2 of the service 



### Before you begin
- [Setup Istio](../install/README.md)
- [Deploy the Bookinfo sample application.](../BookInfo/README.md)

```
# Setup default destination rules - Create subsets in destination rules to use the versions of the  service.
kubectl apply -f destination-rule-all.yaml

# View the destination/subsets rules
kubectl get destinationrules -o yaml
```

### Route to version 1
Route the traffic to version 1 of the microservice. Traffic doesn't reach to other version of the MS 
- productpage >> reviews:v1 (for everyone)

```
# Deploy Virtual service to define route rules, all traffic to v1 of each microservice
kubectl apply -f virtual-service-all-v1.yaml
```

### Route based on the user Identity 
Route the traffic coming from Jason users to version 2 of the review service. Other users will follow the above rule
- productpage >> reviews:v2 >> ratings (only for user jason)
- productpage >> reviews:v1 (for everyone else)

```
# All traffic from a user named Jason will be routed to the service reviews:v2.
kubectl apply -f virtual-service-reviews-test-v2.yaml
```


### Cleanup 
```
kubectl delete -f virtual-service-reviews-test-v2.yaml
kubectl delete -f virtual-service-all-v1.yaml
kubectl delete -f destination-rule-all.yaml
```

### Reference links:

- [Istio Request Routing using Virtual Service](https://istio.io/latest/docs/tasks/traffic-management/request-routing/)
- [Istio getting started official guide](https://istio.io/latest/docs/setup/getting-started/)
