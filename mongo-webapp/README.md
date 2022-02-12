### mongo-webapp:  Sample MongoDB - Webapp Application
Reference -  https://www.youtube.com/watch?v=s_o8dwzRlu4&list=PUdngmbVKX1Tgre699-XLlUA&index=7

Sample Webapp loads data from Mong DB database  
Two deployments, one pod of each - webapp and Mongo DB

##### Steps
Create ConfigMap: mongo-config.yaml 
kind: ConfigMap, reference to mongo-service (defined inside mongo.yaml file)

Create Secret: mongo-secret.yaml for mongoDB base64 encoded username and password
Base64 encoding ex: echo -n mongouser > base64
kind: Secret,  referenced by deploymentTwo deployments, one pod of each - webapp and Mongo DB

Create mongo Deployment + service: mongo.yaml for internal mongo db application
kind: deployment, mongo db container setup, ref to secrets for mongo user/pwd creation
kind: service, mongo db port mapping info

Create webapp Deployment + service: webapp.yaml for external facing web application 
kind: deployment, webapp container setup, ref to secrets for mongo user/pwd to access mongo DB, refer to configmap for mongo DB URL
kind: service, web app port mapping info, nodePort externally exposes the app (default is internal only, there is a predefined range for nodeport)
	
##### Deployment
kubectl apply -f mongo-config.yaml  
kubectl apply -f mongo-secret.yaml  
kubectl apply - f mongo.yaml  
kubectl apply - f webapp.yaml  
