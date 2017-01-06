Create a Configmap from the custom Go template provided. This configuration will be mounted into the Pod and generated each time a new service is added.

```
oc create configmap customrouter --from-file=haproxy-config.template
```


Get the value of the environment namespace (OpenShift project name) via the command `oc project`, and the Service account that is created in the project by default `oc get sa` (in format rhmap-user-<env-name>). Use these values in the placeholders in the following command:

```
sudo oc new-app -f router-template.json -n <environment-project-namespace> --param=ENVIRONMENT_NAMESPACE=<environment-project-namespace> --param=PROJECT_SERVICE_ACCOUNT=<environment-project-environment-name>
```
For example :

```
sudo oc new-app -f router-template.json -n rhmap-rhmap-myEnvironment --param=ENVIRONMENT_NAMESPACE=rhmap-rhmap-myEnvironment --param=PROJECT_SERVICE_ACCOUNT=rhmap-user-myEnvironment
```
