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

> facts: ingress controler is prefered, mysql will not be exposed.

## Mysql
this deployment is for **Development** usage and not **Production**.
feel free to test cloud or any other database provider for your production.

### mysql deployment
this deployment will setup a single mysql instance for dev use.
this deployment:
* uses mysql 8 as base image.
* reads root initial password from kubernetes secrets.
* uses volumes created using mysql-pv.yml configuration.

> mysql deployment, service and secrets are simple and basic, no details included, goes the same with PV and PVC.


