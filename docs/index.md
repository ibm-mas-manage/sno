# Single Node OpenShift (OpenShift 4.10+) Technology Preview

## MAS 8.8/Manage 8.4

This page documents how to run mas manage a sno cluster with ipi/ocp 4.8 in AWS. The practice is based on a M5.4xlarge EC2 instance with mongo, db2u, db2w db, ibm sls, ibm bas, mas core, mas manage deployed. 

## AWS
- Download [aws cli](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- Use aws log on your account

### Create a sno cluster by openshift-install
- Download `openshift-install` and `pull` secret via this [link](https://console.redhat.com/openshift/install/aws/installer-provisioned)
- create install-config.yaml under folder called sno
	- Remove the worker nodes part
	- Use networkType: OpenShiftSDN. Do NOT use OVNKubernetes which has a conflict with IBM SLS install. 

Here is sample install-config.yaml

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

For example, region : us-east-2

- use `openshift-install create cluster --dir=sno`


##Install MAS and dependencies



##Install Manage