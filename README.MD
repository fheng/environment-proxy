
### Setup

Create a Configmap from the custom Go template provided. This configuration will be mounted into the Pod and generated each time a new service is added.

```
oc create configmap customrouter --from-file=haproxy-config.template
```


Get the value of the environment namespace (OpenShift project) via the command `oc project` and use this in the placeholders in the following command:

```
sudo oc new-app -f router-template.json -n <environment-project-namespace> --param=ENVIRONMENT_NAMESPACE=<environment-project-namespace>
```

For example :

```
sudo oc new-app -f router-template.json -n rhmap-rhmap-myrouter --param=ENVIRONMENT_NAMESPACE=rhmap-rhmap-myrouter
```

#### Logging

By default, the HAProxy image has no logging enabled and requires a syslog server to send the logs to. There is an rsyslog server template which will create a "logger" Pod and Service which can be used when HAProxy logging is required. To run the logger :

```
sudo oc new-app -f rsyslog/syslog-server.json -n rhmap-rhmap-myrouter
```

The container needs to be run as root, so to allow this run the following command where rhmap-rhmap-myrouter is <environment-project-namespace> as before :

```
sudo oadm policy add-scc-to-user anyuid system:serviceaccount:rhmap-rhmap-myrouter:logger
```

The environment-proxy Pod needs to be aware that logging has been enabled. This can be done in two ways

1. If the Pod is already running, add the ROUTER_SYSLOG_ADDRESS environment variable as logger:514
2. If the logger Pod is created first, when creating the HAProxy application, append the ROUTER_SYSLOG_ADDRESS=logger:514 to the command shown in setup.
