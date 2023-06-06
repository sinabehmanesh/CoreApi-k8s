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

### CoreApi Deployment
CoreAPI-deployment.yml will deploy a set of pods maintaining CoreApi image.
this deployment:
* deploy pods based on roll out strategy.
* use CoreApi lates image as main container.
* test mysql app before starting the main container.
* performe health checks over **URLS**(readinees and liveness).
* set resource limit and request for hardware consumption.
* run post exec command after startup for laravel database migration.

read configuration file to find out more about replica configuration and deployment stats.

### CoreApi Service
a clusterIP service is designed for ingress use, this service is mapped to ingress for accepting incoming traffic from internet.
this service:
* map containerPort 8000 to 80.
* after deployment, ready to accept traffic from ingress
