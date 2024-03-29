# Integration Manager Umbrella Chart  
This chart is not intended for production use. This serves as an example for how Integration Manager can be configured to run on Kubernetes.

## Overview
This chart installs the following components with no cloud dependencies required
- Integration Manager
- DataCloud Worker
- RabbitMQ
- NGINX Ingress
- MySQL
- Minio

Data Persistence is intentionally disabled for ease of configuration. Recycling MinIO, RabbitMQ or MySQL will results in data loss.
- MySQL is used for all of the configuration data (Accounts, Users, Job Templates, Job Configurations...etc)
- MinIO is used for file storage (djars, maps, processes, job logs...etc)  
- RabbitMQ is used for messaging between IM components

To learn more about Umbrella charts, refer to the link below:  
https://helm.sh/docs/topics/charts/

## Prerequisites:  
- helm3
- Last tested against K8s version
    - Client Version: version.Info{Major:"1", Minor:"21", GitVersion:"v1.21.2", GitCommit:"092fbfbf53427de67cac1e9fa54aaa09a28371d7", GitTreeState:"clean", BuildDate:"2021-06-16T12:52:14Z", GoVersion:"go1.16.5", Compiler:"gc", Platform:"darwin/amd64"}
    - Server Version: version.Info{Major:"1", Minor:"21", GitVersion:"v1.21.1", GitCommit:"5e58841cce77d4bc13713ad2b91fa0d961e69192", GitTreeState:"clean", BuildDate:"2021-05-21T23:01:33Z", GoVersion:"go1.16.4", Compiler:"gc", Platform:"linux/amd64"}

## Installation  

### Add helm repositories  
Run the following commands to add helm repositories  
- ```helm repo add actian-datacloud https://s3.amazonaws.com/actian-datacloud-helm-charts```

### Pull helm dependencies  
From project root run ```helm dependency update```  

### Create Registry Secrets    
Create docker registry secret below (The name used below is already configured for you in the default values file!!!! Changing the names will require additional configuration in your override values file):  
- Actian Dockerhub Registry Key  
```kubectl create secret docker-registry actian-registrykey --docker-username=<username> --docker-password=<password>```  

### Configuring values for your environment  
Refer to override-values.yaml and configure according to the comments. Do not modify the default values.yaml file with your custom values. Use override-values.yaml for that.

### Install  
From the root directory run the command below. Add the -f flag if you created an override-values.yaml file and point to it.
All of the default values are set up under the assumption that the release name is 'integration-manager'. If you install with a different release name, the chart will not run as is. 
```helm install integration-manager . -f override-values.yaml``` 

## Exposing the APIs outside of the cluster with NGINX  
```kubectl port-forward svc/integration-manager-ingress-nginx-controller 8080:80 --address 0.0.0.0```  

Integration Manager Console will be available at:
- localhost:8080/dx

Integration Manager API Documentation will be available at:
- localhost:8080/apidocs

Integration Manager APIs will be available at:
- localhost:8080/api 

Default credentials are: 
- Username: admin
- Password: admin
