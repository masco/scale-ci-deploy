# OpenShift 4 IPI AWS Install Documentation

The OpenShift 4 IPI AWS install playbook is `OCP-4.X/install-on-aws.yml` and will deploy a cluster on AWS. In addition to installing a cluster, the playbook can also perform day 2 operations to include deploying three infra nodes and deploying a workload node to isolate workload driver pods from [openshift-scale/workloads](https://github.com/openshift-scale/workloads) repo.

## Usage

Running from the CLI:

```sh
$ cp OCP-4.X/inventory.example inventory
$ # Edit inventory and add your expected orchestration host
$ # Edit deployment variables (Ex vi OCP-4.X/vars/install-on-aws.yml) or define env variables
$ ansible-playbook -v -i inventory OCP-4.X/install-on-aws.yml
```

## Example

[aws-example.sh](aws-example.sh) is an example script to run this playbook without modifying the vars file.

In order to execute it you should:
* Verify your ssh keys - `PUBLIC_KEY` and `PRIVATE_KEY`
* Verify `OPENSHIFT_CLIENT_LOCATION` is valid (or update to a newer client location)
* Set the release image payload `OPENSHIFT_INSTALL_RELEASE_IMAGE_OVERRIDE`
* Populate `OPENSHIFT_INSTALL_PULL_SECRET`, `OPENSHIFT_INSTALL_QUAY_REGISTRY_TOKEN`, and `OPENSHIFT_INSTALL_REGISTRY_TOKEN`
* Populate `AWS_PROFILE`, `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `AWS_REGION`
* Populate `OPENSHIFT_BASE_DOMAIN`

## Environment variables

### PUBLIC_KEY
Default: `~/.ssh/id_rsa.pub`
Public ssh key file for Ansible.

### PRIVATE_KEY
Default: `~/.ssh/id_rsa`
Private ssh key file for Ansible.

### ORCHESTRATION_USER
Default: `root`
User for Ansible to log in as. Must authenticate with PUBLIC_KEY/PRIVATE_KEY.

### OPENSHIFT_CLEANUP
Default: `false`
Controls if previously deployed cluster will be destroyed prior to installing a new one.

### OPENSHIFT_INSTALL
Default: `true`
Controls if cluster create and install portion of playbook is ran. The only reason to disable this is if you have a cluster already and wanted to run `OPENSHIFT_POST_INSTALL`, `OPENSHIFT_POST_CONFIG`, and/or `OPENSHIFT_DEBUG_CONFIG` against an existing cluster.

### OPENSHIFT_POST_INSTALL
Default: `true`
Controls if day 2 operations are ran against this cluster. Day 2 operations includes creating infra nodes, creating a workload node, adding remote write config and steering infra workloads to infra nodes.

### ENABLE_DITTYBOPPER
default: `true`
Controls wheather a mutable Grafana instance is launched.

### ENABLE_REMOTE_WRITE
Default: `false`
Controls whether remote write should be enabled or not, only applicable when OPENSHIFT_POST_INSTALL is set to true and also will need to set SINCGARS_REMOTE_WRITE_URL when this var is set to true.

### SINCGARS_CLUSTER_NAME
Default: value of label machine.openshift.io/cluster-api-cluster
This is an identifier to associate system metrics

### SINCGARS_REMOTE_WRITE_URL
Default: no default
This is the connection url that will be used for remote write, must set this when ENABLE_REMOTE_WRITE is set and OPENSHIFT_POST_INSTALL is set

### OPENSHIFT_POST_CONFIG
Default: `true`
Controls if "post-config" options are ran for this specific cluster. This opens the security groups for this cluster to permit more network tests to execute from the [openshift-scale/workloads](https://github.com/openshift-scale/workloads) repo.

### OPENSHIFT_DEBUG_CONFIG
Default: `false`
This enables easier debugging for a cluster by populating the initially installed cluster nodes with the kubeconfig and ssh keys for quicker out of band and hands on debugging.

### OPENSHIFT_CLIENT_LOCATION
Default: No default.
Location to download and unpack the OpenShift client tool `oc`. The latest client can be found [https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/) or [https://mirror.openshift.com/pub/openshift-v4/clients/ocp-dev-preview](https://mirror.openshift.com/pub/openshift-v4/clients/ocp-dev-preview)

### OPENSHIFT_INSTALL_RELEASE_IMAGE_OVERRIDE
Default: No default.
The release image override payload. Also where the install `openshift-install` binary is extracted from. Find the latest test images at [https://openshift-release.svc.ci.openshift.org/](https://openshift-release.svc.ci.openshift.org/)

### OPENSHIFT_INSTALL_BINARY_URL
Default: No default.
Link to the binary url tarball to extract the openshift-install from. Find the latest builds at [https://mirror.openshift.com/pub/openshift-v4/clients/ocp-dev-preview](https://mirror.openshift.com/pub/openshift-v4/clients/ocp-dev-preview)

### OPENSHIFT_INSTALL_APIVERSION
Default: `v1`
Depending upon the version of the payload tested this version string might need to be adjusted.

### OPENSHIFT_INSTALL_SSH_PUB_KEY_FILE
Default: `~/.ssh/id_rsa.pub`
Public ssh key file to be used in the install-config.yaml.

### OPENSHIFT_INSTALL_PULL_SECRET
Default: No default.
The pull secret to be used in installing the cluster.

### OPENSHIFT_INSTALL_QUAY_REGISTRY_TOKEN
Default: No default.
The token used to download and extract the installer binary from quay.

### OPENSHIFT_INSTALL_IMAGE_REGISTRY
Default: `registry.svc.ci.openshift.org`
The registry which contains the install image.

### OPENSHIFT_INSTALL_REGISTRY_TOKEN
Default: No default.
The token used to download and extract the installer binary from the registry.

### OPENSHIFT_INSTALL_INSTALLER_FROM_SOURCE
Default: `false`
Option to build the installer binary from source instead of extract method. Not recommended.

### OPENSHIFT_INSTALL_INSTALLER_FROM_SOURCE_VERSION
Default: `master`
Branch to build installer from if `OPENSHIFT_INSTALL_INSTALLER_FROM_SOURCE` is set to true.

### GOPATH
Default: `/root/.go`
When building from source the gopath must be set.

### AWS_PROFILE
Default: `default`
The name of the AWS profile to be set on the orchestration machine for use with the AWS cli and installer.

### AWS_ACCESS_KEY_ID
Default: No default.
The AWS access key.

### AWS_SECRET_ACCESS_KEY
Default: No default.
The AWS secret access key.

### AWS_REGION
Default: No default.
The AWS region to install on to.

### OPENSHIFT_BASE_DOMAIN
Default: No default.
The base domain for the cluster.

### OPENSHIFT_CLUSTER_NAME
Default: No default.
The name of the cluster.

### OPENSHIFT_MASTER_COUNT
Default: `3`
The number of master nodes.

### OPENSHIFT_WORKER_COUNT
Default: `5`
The number of worker nodes to install.

### OPENSHIFT_MASTER_INSTANCE_TYPE
Default: `m5.xlarge`
The instance type of the masters.

### OPENSHIFT_WORKER_INSTANCE_TYPE
Default: `m5.large`
The instance type of the worker nodes.

### OPENSHIFT_MASTER_ROOT_VOLUME_SIZE
Default: `64`
The root volume disk size for the masters.

### OPENSHIFT_MASTER_ROOT_VOLUME_TYPE
Default: `gp2`
The root volume type for the masters. Can be `gp2` or higher performing `io1` volume types.

### OPENSHIFT_MASTER_ROOT_VOLUME_IOPS
Default: `0`
When `OPENSHIFT_MASTER_ROOT_VOLUME_TYPE` is set to `io1`, the number of iops can be set here.

### OPENSHIFT_WORKER_ROOT_VOLUME_SIZE
Default: `64`
The root volume size for worker nodes.

### OPENSHIFT_WORKER_ROOT_VOLUME_TYPE
Default: `gp2`
The root volume type for worker nodes. Can be `gp2` or higher performing `io1` volume types.

### OPENSHIFT_WORKER_ROOT_VOLUME_IOPS
Default: `0`
When `OPENSHIFT_WORKER_ROOT_VOLUME_TYPE` is set to `io1`, the number of iops can be set here.

### OPENSHIFT_CIDR
Default: `10.128.0.0/14`
The block of IP addresses from which Pod IP addresses are allocated.

### OPENSHIFT_MACHINE_CIDR
Default: `10.0.0.0/16`
The block of IP addresses used for hosts.

### OPENSHIFT_SERVICE_NETWORK
Default: `172.30.0.0/16`
The block of IP addresses for services.

### OPENSHIFT_HOST_PREFIX
Default: `23`
The subnet prefix length to assign to each individual node for Pod IP addresses.

### OPENSHIFT_NETWORK_TYPE
Default: `OpenShiftSDN`
The network type used for the cluster, for OVN set it to `OVNKubernetes`.

### OPENSHIFT_POST_INSTALL_POLL_ATTEMPTS
Default: `600`
The number of times to poll to check while the infra and workload node are being created and added to the cluster.

### OPENSHIFT_TOGGLE_INFRA_NODE
Default: `true`
Enable infra nodes to be created with the `OPENSHIFT_POST_INSTALL` step.

### OPENSHIFT_TOGGLE_WORKLOAD_NODE
Default: `true`
Enable a workload node to be created with the `OPENSHIFT_POST_INSTALL` step.

### KUBECONFIG_PATH
Default: `~/.kube/config`
Path to kube_config.

### CERBERUS_CONFIG_PATH
Default: `~/cerberus.yaml`
Path to cerberus_config.

### CERBERUS IMAGE
Default: `quay.io/openshift-scale/cerberus:latest`
Image to be pulled to run the containerized version of cerberus.

### CERBERUS_URL
Default: "http://0.0.0.0:8080"
Optional arguement for cerberus configuration if cerberus is using a different URL than the default.

### DISTRIBUTION
Default: `openshift`
Distribution type supported by cerberus can be kubernetes or openshift.

### PORT
Default: `8080`
http server port where cerberus status is published.

### WATCH NODES
Default: `true`
Set to True for the cerberus to monitor the cluster nodes.

### WATCH CLUSTER OPERATORS
Default: `true`
Set to True for the cerberus to monitor the cluster operators.

### WATCH_URL_ROUTES
Default: `[]`
Route url's to be monitored by cerberus.

### WATCH NAMESPACES
Default: `'[openshift-etcd, openshift-apiserver, openshift-kube-apiserver, openshift-monitoring, openshift-kube-controller-manager, openshift-machine-api, openshift-kube-scheduler, openshift-ingress, openshift-sdn, openshift-ovn-kubernetes]'`
List the namespaces to be monitored by cerberus.

### CERBERUS_PUBLISH_STATUS
Default: `true`
When enabled, cerberus starts a light weight http server and publishes the status.

### INSPECT_COMPONENTS
Default: `false`
Enable it only when OpenShift client is supported to run. When enabled, cerberus collects logs, events and metrics of failed components.

### PROMETHEUS_URL
Default: `''`
The prometheus url.

### PROMETHEUS_BEARER_TOKEN
Default: `''`
The prometheus bearer token is needed to authenticate with prometheus.

### SLACK_INTEGRATION
Default: `false`
When enabled, cerberus reports the the failed interations and sends the report for the same on a slack channel.

### SLACK_API_TOKEN
Default: No default.
It refers to Bot User OAuth Access Token used for cerberus.

### SLACK_CHANNEL
Default: No default.
It refers to the slack channel ID the user wishes to receive the notifications for cerberus failures.

### WATCHER_SLACK_ID
Default: `'{Monday: , Tuesday: , Wednesday: , Thursday: , Friday: , Saturday: , Sunday: }'`
When slack_integration is enabled, a watcher can be assigned for each day. The watcher of the day is tagged while reporting failures in a slack channel. Values are slack member ID's.

### SLACK_TEAM_ALIAS
Default: No default.
The slack team alias to be tagged while reporting failures in the slack channel when no watcher is assigned.

### ITERATIONS
Default: `5`
Iterations to loop before stopping the watch in cerberus monitoring. It will be replaced with infinity when the daemon mode is enabled.

### SLEEP_TIME
Default: `60`
Sleep duration between each iteration in cerberus monitoring.

### KUBE_API_REQUEST_CHUNK_SIZE
Default: `250`
Large requests will be broken into the specified chunk size to reduce the load on API server and improve responsiveness while retrieving node and pod status in cerberus.

### DAEMON_MODE
Default: `true`
Iterations are set to infinity which means that the cerberus will monitor the resources forever.

### CORES_USAGE_PERCENTAGE
Default: `0.5`
The fraction of cores to be used for multiprocessing.

### DATABASE_PATH
Default: `/tmp/cerberus.db`
Path where cerberus database needs to be stored.

### REUSE_DATABASE
Default: `false`
When enabled, cerberus database is reused to store the failures.

### CERBERUS_ENABLE
Default: `false`
Controls whether the monitoring of Kubernetes/OpenShift cluster components by cerberus should be enabled or not.

### MACHINESET_METADATA_LABEL_PREFIX
Default: `machine.openshift.io`
The prefix used in machinesets. Usually this is `machine.openshift.io` however it might be `sigs.k8s.io` depending on version installed.

### OPENSHIFT_INFRA_NODE_INSTANCE_TYPE
Default: `m5.large`
The instance type for infra nodes.

### OPENSHIFT_WORKLOAD_NODE_INSTANCE_TYPE
Default: `m5.large`
The instance type for the workload node.

### OPENSHIFT_INFRA_NODE_VOLUME_SIZE
Default: `64`
The root volume size for the infra nodes.

### OPENSHIFT_INFRA_NODE_VOLUME_TYPE
Default: `gp2`
The root volume type for infra nodes. Can be `gp2` or higher performing `io1` volume types.

### OPENSHIFT_INFRA_NODE_VOLUME_IOPS
Default: `0`
When `OPENSHIFT_INFRA_NODE_VOLUME_TYPE` is set to `io1`, the number of iops can be set here.

### OPENSHIFT_WORKLOAD_NODE_VOLUME_SIZE
Default: `64`
The root volume size for the workload node.

### OPENSHIFT_WORKLOAD_NODE_VOLUME_TYPE
Default: `gp2`
The root volume type for workload node. Can be `gp2` or higher performing `io1` volume types.

### OPENSHIFT_WORKLOAD_NODE_VOLUME_IOPS
Default: `0`
When `OPENSHIFT_WORKLOAD_NODE_VOLUME_TYPE` is set to `io1`, the number of iops can be set here.

### OPENSHIFT_PROMETHEUS_RETENTION_PERIOD
Default: `15d`
The retention period for the Prometheus server.

### OPENSHIFT_PROMETHEUS_STORAGE_CLASS
Default: `gp2`
The storage class for Prometheus server.

### OPENSHIFT_PROMETHEUS_STORAGE_SIZE
Default: `10Gi`
The storage size for Prometheus server.

### OPENSHIFT_ALERTMANAGER_STORAGE_CLASS
Default: `gp2`
The storage class for the alertmanager servers.

### OPENSHIFT_ALERTMANAGER_STORAGE_SIZE
Default: `2Gi`
The storage size for the alert manager servers.

### KUBECONFIG_AUTH_DIR_PATH
Default: No default.
For use with Flexy built clusters, in which only the post-install steps are ran

### DATA_SERVER_ENABLE
Default: `false`
Controls whether the data server is launched.

### DATA_SERVER_IMAGE
Default: `quay.io/openshift-scale/snappy-data-server`
Latest, maintained image of the snappy data server.

### DATA_SERVER_LOG_LVL
Default: `info`  
Data server log level. Current [Uvicorn server](https://www.uvicorn.org) **options:** *'critical', 'error', 'warning', 'info', 'debug', 'trace'.

### DATA_SERVER_PORT
Default: `7070`  
Data server service port.

### DATA_SERVER_PUBLIC_HOST
Default: `localhost`  
URL to public host of data server.

### DATA_SERVER_ROOT_PATH
Default: `~/data_server`
Absolute path to the data server's host's directory.

### DATA_SERVER_SECRET
Default: No default.
Secret to encode passwords in database.

### FIRST_SUPERUSER
Default: No default.
Username for the first super user.

### FIRST_SUPERUSER_PASSWORD
Default: No default.
Password for the first super user.

### POSTGRES_PASSWORD
Default: No default.
Postgresql database super user password.
