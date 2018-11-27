---
layout: blog
title: 'AWS EBS CSI Driver Alpha is Available'
date: 2018-11-18
---

**Author**: Cheng Pan (leakingtapan), Fabio Bertinatto (bertinatto)

As [Container Storage Interface (CSI)](https://github.com/container-storage-interface/spec/tree/master) lands 1.0, different storage providers (SP) start onboarding this new specification and future development will be shifted towards CSI as new features are developed. CSI specification enables both container orchestrators (CO) and SP to evolve independently in a modular way.

As one of the pioneers in the space, we are glad to announce that [AWS Elastic Block Store](https://aws.amazon.com/ebs/) (EBS) CSI Driver is now in alpha.

# Features

The EBS CSI driver implements basic operations required by the CSI specification to provision, attach and mount volumes into Pods and the reverse operations to remove it. The existing workflow of static provisioning or dynamic provisioning should stay almost unchanged, as the only difference is to use the CSI driver as the provisioner. The following excert is an example of a *storageclass* using our driver:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: csi-sc
provisioner: ebs.csi.aws.com
```

To pair with the current in-tree EBS volume plugin, all the *storageclass* parameters are supported, including `type`, `fsType`, `iopsPerGB`, `encrypted` and `kmsKeyId`. The exception is `zone/zones`, which is not included because it has been deprecated in Kubernetes v1.12 in favor of `allowedTopologies`.

In addition to that, if you are building a multi-zone application that requires provisioning a volume in different availability zones, the [Topology-Aware Volume Provisioning](https://kubernetes.io/blog/2018/10/11/topology-aware-volume-provisioning-in-kubernetes/) feature could be turned on by setting the `volumeBindingMode` field:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: csi-sc
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer
```

# How can I use it?

There is quite a few steps that you need to follow in order to use the EBS CSI driver. In short:
1. Deploy the driver - this includes deploying several sidecar containers and creating several service accounts and cluster roles.
1. Create a *storageclass* with the _provisioner_ set to use the EBS CSI driver, like in the example above.
1. Create a Persistence Volume Claim (PVC) that references the created *storageclass*. Also, a new Persistence Volume (PV) will be created. Once created, it will be bound to the PVC.
1. Deploy the Pod that uses the PVC mentioned above.

To simplify this process we created some sample manifest files that can be accessed at the [installation](https://github.com/kubernetes-sigs/aws-ebs-csi-driver#installation) page. For more details, please refer to the [README](https://github.com/kubernetes-sigs/aws-ebs-csi-driver) in our Github page.

# What's next?

We are planning to harden the driver in next Kubernetes release cycle 2019 Q1 and have a beta release by then. Among the features planned is support to snapshots and support for the recently released CSI 1.0.

For a complete list of planned features, please refer to our [Milestones](https://github.com/kubernetes-sigs/aws-ebs-csi-driver/milestones) page.

# How can I get involved?

Help can come in many forms and we are happy to accept it in any way you want! We are always looking for new contributors, so feel free to contact us if you are interested in helping us out. If you encounter any issues, please report them in our [Issues](https://github.com/kubernetes-sigs/aws-ebs-csi-driver/issues) page.

A special thanks to everyone who helped to make this happen, including Fabio Bertinatto ([bertinatto](https://github.com/bertinatto/)), Cheng Pan ([leakingtapan](https://github.com/bertinatto/)) and Nishi Davidson ([d-nishi](https://github.com/d-nishi)).