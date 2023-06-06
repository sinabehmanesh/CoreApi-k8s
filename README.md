# Laravel-deployment
### laravel project deployment to k8s cluster

This is a Basic **Laravel** project Ready to be deployed to Kubernetes Cluster alongside:
* Nginx Ingress
* Single instance Mysql
* laravel FPM Api

## CoreApi
CoreApi Directory hosts the source code with all of the configurations required.
it does host a Dockerfile that is a Container in API Deployment.
.env file can be configured using configMap.

### CoreApi Dockerfile
Core api dockerfile is using php frpm as base Image(**8.2**)
you can add php extentions and more configuration as you need.
feel free to add more content such as changing user, workdir, multistaging and etc.

## API
API directory is where the laravel api deployment and its service is configured.

### coreapi deployment
CoreAPI-deployment.yml will deploy a set of pods maintaining CoreApi image.
this deployment:
* deploy pods based on roll out strategy.
* use CoreApi lates image as main container.
* test mysql app before starting the main container.
* performe health checks over **URLS**(readinees and liveness).
* set resource limit and request for hardware consumption.
* run post exec command after startup for laravel database migration.

read configuration file to find out more about replica configuration and deployment stats.

### coreapi service
a clusterIP service is designed for ingress use, this service is mapped to ingress for accepting incoming traffic from internet.
this service:
* map containerPort 8000 to 80.
* after deployment, ready to accept traffic from ingress.

## Ingress
ingress.yml file is where you can talk to internet about your CoreApi service.
we do not have a Host so the ingress will route all the incoming traffic to the backend. backend is marked as CoreApi-service.
check Ingress config file for more info.

>  ingress controler is prefered, mysql will not be exposed.

## Mysql
this deployment is for **Development** usage and not **Production**.
feel free to test cloud or any other database provider for your production.

### mysql deployment
this deployment will setup a single mysql instance for dev use.
this deployment:
* uses mysql 8 as base image.
* reads root initial password from kubernetes secrets.
* uses volumes created using mysql-pv.yml configuration.

> mysql deployment, service and secrets are simple, no details included, goes the same with PV and PVC.


## HPA
you can configure HPA object or just simply enable horizontal auto scaling for your deployments.
if so, in mannual approach, use this command to enable autoscale for CoreApi Deployment:
 <br />
`kubectl autoscale deployment CoreApi --cpu-percent=50 --min=4 --max=10 `
 <br />
this will autoscale your deployment based on CPU usage, with maximum of 10 pods.
check HPA status using: `kubectl get hpa`
 <br />
**or**
 <br />
you can deploy a HPA object to take care of this job:

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: CoreApi
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: CoreAPI
  minReplicas: 4
  maxReplicas: 10
```

## Notes:
This configuration supports helm improvements and various CICD pipelines.
for this project, php version is 8.2 and mysql is 8.0. you can simply change them in configuration. <br />
thats all.
change the deployment rollout strategy to meet your requirements.