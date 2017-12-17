# Notes from the book DevOps with OpenShift

# By Stefano Picozzi, Mike Hepburn & Noel O'Connor

# O'Reilly 2017

# http://github.com/devops-with-openshift

- OpenShift is a computer software product from Red Hat for container-based software deployment and management. It is a supported distribution of Kubernetes using Docker containers and DevOps tools for accelerated application development.

- OpenShift Origin is the upstream community project used in OpenShift Online, OpenShift Dedicated, and OpenShift Container Platform. Built around a core of Docker container packaging and Kubernetes container cluster management, Origin is augmented by application lifecycle management functionality and DevOps tooling. Origin provides an open source application container platform. All source code for the Origin project is available under the Apache License (Version 2.0) on GitHub.

- OpenShift Online is Red Hat's public cloud application development and hosting service.

- Online offered version 2 of the Origin project source code, which is also available under the Apache License Version 2.0. This version supported a variety of languages, frameworks, and databases via pre-built "cartridges" running under resource-quota "gears". Developers could add other language, database, or components via the OpenShift Cartridge application programming interface. This was deprecated in favour of OpenShift 3 and will be withdrawn on 30th September 2017 for non-paying customers and 31st December 2017 for paying customers.

- OpenShift 3 is built around Kubernetes. It can run any Docker-based container, but Openshift Online is limited to running containers that do not require root.

- OpenShift Dedicated is Red Hat's managed private cluster offering, built around a core of application containers powered by Docker, with orchestration and management provided by Kubernetes, on a foundation of Red Hat Enterprise Linux. It's available on the Amazon Web Services (AWS) and Google Cloud Platform (GCP) marketplaces.

- OpenShift Container Platform (formerly known as OpenShift Enterprise) is Red Hat's on-premises private platform as a service product, built around a core of application containers powered by Docker, with orchestration and management provided by Kubernetes, on a foundation of Red Hat Enterprise Linux.

```
oc version

docker version

oc cluster up \
  --host-data-dir=... \
  --host-config-dir=...

oc cluster down

oc cluster up \
  --host-data-dir=... \
  --host-config-dir=... \
  --use-existing-config
```

```
oc login -u developer -p developer

oc project myproject

oc new-app --name='cotd' --labels name='cotd' php~https://github.com/devops-with-openshift/cotd.git -e SELECTOR=cats

oc expose service cotd
```

## Create persistent volume


## Create volume claim


## Deployments

### The replication controller

### Deployment strategies

#### Rolling

#### Triggers

#### Recreate

#### Custom

#### Lifecycle hooks

#### Deployment Pod Resources

### Blue-Green deployments

```
oc new-app https://github.com/devops-with-openshift/bluegreen#green --name=green

oc patch route/bluegreen -p '{"spec":{"to":{"name":"green"}}}'

oc patch route/bluegreen -p '{"spec":{"to":{"name":"blue"}}}'
```

### A/B Deployments

```
oc annotate route/ab haproxy.router.openshift.io/balance=roundrobin

oc set route-backends ab cats=100 city=0

oc set route-backends ab --adjust city=+10%
```

### Canary Deployments

### Rollbacks

```
oc rollback cotd --to-version=1 --dry-run

oc rollback cotd --to-version=1

oc describe dc cotd
```


## Pipelines

### Jenkins template

- Comes with all necessary OpenShift plugins (OpenShift login, OpenShift sync, OpenShift pipeline, Kubernetes)

- Comes with example `Jenkinsfile`

```
oc get templates -n openshift | grep jenkins-pipeline-example

oc new-app jenkins-ephemeral # to keep the logs when Jenkins container shuts down

oc get pods

oc new-app jenkins-pipeline-example

oc start-build sample-pipeline

oc get pods
```

- Customizing Jenkins:

```
vim openshift.local.config/master/master-confi.yaml

jenkinsPipelineConfig:
  autoProvisionEnabled: true
  parameters:
    JENKINS_IMAGE_STREAM_TAG: jenkins-2-rhel7:latest
    ENABLE_OAUTH: true
  serviceName: jenkins
  templateName: jenkins-ephemeral
  templateNamespace: openshift
  ```
  
  - Good resource for Jenkinsfiles: https://github.com/fabric8io/fabric8-jenkinsfile-library
  
  ## Configuration Management