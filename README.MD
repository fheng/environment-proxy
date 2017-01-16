# RHMAP Environment Proxy

This project enables the use of Non-SSL Wildcard certificates when running RHMAP on the OpenShift Container Platform. It deploys a custom OpenShift router inside a RHMAP Environment Project, which works as a proxy to forward requests to the applications and services running within that environment.

## Usage

When there is a specific requirement which disallows the use of wildcard certificates, this application should be deployed inside the per-environment project, created by RHMAP on OpenShift when an environment is created via the studio. The template will create a deployment configuration and service called `environment-proxy` in the OpenShift project. A route will also be created which must be configured with an SSL certifcate by the end user.

Once applications are deployed to this environment, the environment-proxy application will learn about them automatically, and they will be reachable via the proxy. This negates the need for an SSL certificate to be placed on each applications route.

## Installation

This application relies on the creation of a ConfigMap object. Create this Configmap from the custom Golang template provided, using the following command:

```
oc create configmap customrouter --from-file=haproxy-config.template
```


Get the value of the environment namespace (RHMAP environment project in OpenShift) via the command `oc get projects`. This should take the format `rhmap-rhmap-myEnvironmentName`. We also need to pass the environment name, which is the latter section of the namespace. From the previous example, this would be `myEnvironmentName`. Finally, we are required to pass the domain name with any subdomains where we want the external route for the environment-proxy to be created. For example `my.example.com` will create a route exposing `https://myEnvironment.my.example.com`. Use these values in the placeholders in the following command, which must be run as administrator:

```
sudo oc new-app -f router-template.json -n <environment-project-namespace> --param=ENVIRONMENT_NAMESPACE=<environment-project-namespace> --param=ENVIRONMENT=<environment-name> --param=DOMAIN=<domain>

```

For example :

```
sudo oc new-app -f router-template.json -n rhmap-rhmap-myEnvironmentName --param=ENVIRONMENT_NAMESPACE=rhmap-rhmap-myEnvironmentName --param=ENVIRONMENT=myEnvironment --param=DOMAIN=my.example.com --param=ROUTER_SYSLOG_ADDRESS=logger:514
```

