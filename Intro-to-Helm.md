## Helm
**a) Installation**
```shell
brew install helm
choco install kubernetes-helm
```
**b) Purpose of Helm**
- Helm is a tool for managing Kubernetes packages called `charts`. Helm can do the following:

- Create new charts from scratch
- Package charts into chart archive (tgz) files
- Interact with chart repositories where charts are stored
- Install and uninstall charts into an existing Kubernetes cluster
- Manage the release cycle of charts that have been installed with Helm

**c) Important concepts**
1. The `chart` is a bundle of information necessary to create an instance of a Kubernetes application.
2. The `config` contains configuration information that can be merged into a packaged chart to create a releasable object.
3. A `release` is a running instance of a chart, combined with a specific config.

**d) Components**

1.` Helm Client` - command-line client for end users and responsible for
- local chart development
- managing repositories
- managing releases
- interfacing with the Helm library - sending charts to be installed and requesting for upgrading or uninstalling of existing releases
  
2.` Helm library` - provides logic for executing all Helm operations. It interfaces with the Kubernetes API server and enables
- combining a chart and configuration to build a release
- installing charts into kubernetes, and providing the subsequent release object
- upgrading and uninstalling charts by interacting with kubernetes

The helm client and library are written in go language.

**e) Getting started**

**1) Initialize a Helm chart repository**
- Add a chart repository from [Artifact Hub](https://artifacthub.io/)
```shell
helm repo add bitnami https://charts.bitnami.com/bitnami
```

**2) Search the repo for the chart to install / finding charts**
```shell
helm search repo
helm search hub
helm search repo bitnami
```

**3) Update a repo**
```shell
helm repo update
```

**4) Install a chart** - takes 2 arguments - a release name you pick and chart to install
```shell
helm install mysql bitnami/nginx 
or
helm install bitnami/nginx --generate-name
```

**5) See the features of a chart**
```shell
helm show chart bitnami/nginx
or
helm show all bitnami/nginx
```
Whenever you install a chart, a new release is created. One chart can be installed multiple times into the same cluster.

**6) To see all that has been released**
```shell
helm list
or
helm ls
```

**7) View the status of a release** 
```shell
helm status nginx
```

**8) Uninstall a release**
```shell
helm uninstall nginx
```
This will remove all resources associated with the release as well as the release history. To keep the history, you can use the `--keep-history` flag


**9) Learn more about available commands**
```shell
helm help
or
helm get -h
```

**10) Customizing a chart**
- Installing a chart like above will only use default configuration options for tha chart. To customize it, you can look at the options that are configurable for the chart.
```shell
helm show values bitnami/nginx
```
- You can override the settings by passing a file during installation.

```shell
echo '{mariadb.auth.database: user0db, mariadb.auth.username: user0}' > values.yaml
helm install -f values.yaml bitnami/wordpress --generate-name
```
Two ways to pass configuration data during install

a) `--values` or `-f` to specify a YAML file with overrides
```shell
helm install nginx -f update.yaml bitnami/nginx 
```
b) `--set` to specify overrides on the command line
```shell
helm install nginx --set service.type=NodePort --set service.port=80 bitnami/nginx
```

**11) Helm upgrade**
- When a new version of the chart is released, or when you want to change the configuration of your release, you can use upgrade.
-  An upgrade takes an existing release and upgrades it according to the information you provide
```shell
helm upgrade -f values.yaml mysql bitnami/nginx
helm upgrade nginx --set service.type=loadbalancer --set service.port=80 bitnami/nginx
helm get values mysql # To see whether the new values took effect
```

**12) Helm history**
- To see the revision number of a certain release, we can use
```shell
helm history [RELEASE]
helm history nginx
```
**13) Helm rollback**
- If something does not go as planned during a release, it is easy to roll back to a previous release
```shell
helm rollback [RELEASE] [REVISION]
helm rollback nginx 1
```
-

**14) Helpful options for Install/Upgrade/Rollback**

`--timeout`: A Go duration value to wait for kubernetes commands to complete. Default is` 5m0s`

`--wait`: Waits until all pods are in ready state. It will wait for as long as the `--timeout` value. If timeout is reached, the release will be marked as `FAILED`.

**f) Creating your own charts**

**1) You can create your own chart using create command.**
```shell
helm create wema
```
This will create a chart `./wema` which you can now edit and create your own templates.

**2) You can validate that the chart is well-formed by running**
```shell
helm lint 
```
**3) You can also check the validity of the chart by using the template command**
```shell
helm template [NAME] [CHART]
helm template wema1 wema-0.1.0.tgz
```
**4) You can then package the chart for distribution by running**
```shell
helm package wema
```
**5) You can now install the chart using the `install` command**
```shell
helm install wema ./wema-0.1.0.tgz
```
