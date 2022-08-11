# Single Node OpenShift (OpenShift 4.10+) 
**Technology Preview**

###MAS 8.8/Manage 8.4
This page documents how to setup MAS Manage on a SNO cluster.

### Minimum Requirements
vCPU: 16Cores
RAM: 64Gb

## AWS
This is based on a M5.4xlarge EC2 instance with mongo, db2u, db2w db, ibm sls, mas core, mas manage deployed.  
- Download [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- Use AWS login to your account

### Create SNO cluster
- Download `openshift-install` and `pull` secret via this [link](https://console.redhat.com/openshift/install/aws/installer-provisioned)
- create install-config.yaml under folder called sno
	- Remove the worker nodes part
	- Use networkType: OpenShiftSDN. Do NOT use OVNKubernetes which has a conflict with IBM SLS install. 

Here is a sample install-config.yaml

```
apiVersion: v1
baseDomain: <your base domain>
compute:
- name: worker
  replicas: 0
controlPlane:
  name: master
  platform:
    aws:
      type: m5.4xlarge 
  replicas: 1
metadata:
  name: sno
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  machineNetwork:
  - cidr: 10.0.0.0/16
  networkType: OpenShiftSDN 
  serviceNetwork:
  - 172.30.0.0/16
platform:
  aws:
    region: <your region>
publish: External
pullSecret: '<your secret>'
```

For example, region: us-east-2

- Create cluster
`openshift-install create cluster --dir=sno`

### Delete SNO cluster

- Delete SNO cluster
	- openshift-install delete cluster --dir /root/sno

## Azure

In Progress

## vSphere

In Progress

## Bare Metal

In Progress

## Install MAS and dependencies

- Follow this [link](https://ibm-mas.github.io/ansible-devops/#running-in-docker) to install [Python & Ansible](Install Python & Ansible), [OpenShift CLI](https://ibm-mas.github.io/ansible-devops/#install-openshift-cli)
and the [Ansible Collection](https://ibm-mas.github.io/ansible-devops/#install-the-ansible-collection)

- Prepare for [Core Installation](https://ibm-mas.github.io/ansible-devops/playbooks/oneclick-core/#preparation)
  
### Set Environment variables

```
export MONGODB_STORAGE_CLASS=<storage-class>
export MAS_APP_ID=manage
export SLS_ENTITLEMENT_KEY=<entitlement-key>
export SLS_STORAGE_CLASS=<storage-class>
export SLS_LICENSE_ID=<license-id>
export SLS_LICENSE_FILE=<license-file>
export UDS_STORAGE_CLASS=<storage-class>
export UDS_CONTACT_EMAIL=<your email-id>
exportand Ansible collections (Install the Ansible Collection) UDS_CONTACT_FIRSTNAME=<your first name>
export UDS_CONTACT_LASTNAME=<your first name>
export MAS_INSTANCE_ID=<instance-id>
export MAS_CONFIG_DIR=<config-dir>
export MAS_WORKSPACE_ID=<mas-workspace--d>
export MAS_ENTITLEMENT_KEY=<entitlement-key>
export PROMETHEUS_STORAGE_CLASS=<storage-class>
export PROMETHEUS_ALERTMGR_STORAGE_CLASS=<storage-class>
export GRAFANA_INSTANCE_STORAGE_CLASS=<storage-class>
export MONGODB_REPLICAS=1
```

- Sample
```
export MONGODB_STORAGE_CLASS=gp2
export MAS_APP_ID=manage
export SLS_STORAGE_CLASS=gp2
export SLS_LICENSE_ID=0242ac110002 
export SLS_LICENSE_FILE=~/masconfig/entitlement.lic
export UDS_STORAGE_CLASS=gp2
export UDS_CONTACT_EMAIL=abc@us.ibm.com
export UDS_CONTACT_FIRSTNAME=abc
export UDS_CONTACT_LASTNAME=abc
export MAS_INSTANCE_ID=sno
export MAS_CONFIG_DIR=~/masconfig
export MAS_WORKSPACE_ID=masdev
export MAS_INSTANCE_ID=sno
export PROMETHEUS_STORAGE_CLASS=gp2
export PROMETHEUS_ALERTMGR_STORAGE_CLASS=go2
export GRAFANA_INSTANCE_STORAGE_CLASS=gp2
export MONGODB_REPLICAS=1
```

### MAS core ansible collection

- OC Login: 
```
oc login --token=xxxx --server=<https://myocpserver>
```
Replace `xxxx` with your OpenShift token and `https://myocpserver` with your OpenShift Server.

- Run the Core ansible collection:
```
ansible-playbook ~/.ansible/collections/ansible_collections/ibm/mas_devops/playbooks/oneclick_core.yml
```

## Intall DB2 (optional)

- Install DB2 using this [link](https://ibm-mas.github.io/ansible-devops/roles/db2/)
- Use gp2 as storage class.
- Sample

```
export DB2_BACKUP_STORAGE_ACCESSMODE=READWRITEONCE
export DB2_META_STORAGE_ACCESSMODE=READWRITEONCE
export DB2_META_STORAGE_CLASS=gp2
export DB2_DATA_STORAGE_CLASS=gp2
export DB2_BACKUP_STORAGE_CLASS=gp2
export DB2_LOGS_STORAGE_CLASS=gp2
export DB2_TEMP_STORAGE_CLASS=gp2
```	

## Install Manage

- OC Login: 
```
oc login --token=xxxx --server=<https://myocpserver>
```
Replace `xxxx` with your OpenShift token and `https://myocpserver` with your OpenShift Server.

- Run the Manage ansible collection:

```
ansible-playbook  ~/.ansible/collections/ansible_collections/ibm/mas_devops/playbooks/oneclick_add_manage.yml
```
- Configure the database, if you want to use an existing database.

https://www.ibm.com/docs/en/maximo-manage/continuous-delivery?topic=manage-configuring-database
