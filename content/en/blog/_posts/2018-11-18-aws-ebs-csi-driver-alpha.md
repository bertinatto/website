---
layout: blog
title: 'AWS EBS CSI Driver Alpha is Available'
date: 2018-11-18
---

**Author**: Cheng Pan (leakingtapan), Fabio Bertinatto (bertinatto)

As [Container Storage Interface(CSI)](https://github.com/container-storage-interface/spec/tree/master) lands 1.0, different storage providers(SP) start onboarding this new specification and future development will be shifted toward CSI as new features are developed. CSI specification enables both container orchestrator (CO) and SP to involve independently in a modular way. 

As one of the pioneer in the space, we are glad to announce that [AWS Elastic Block Store](https://aws.amazon.com/ebs/)(EBS) CSI Driver is now in alpha.

# Features

The EBS CSI driver implements basic operations required by the CSI spec to provision, attach and mount volume into Pod and the reverse operations to remove it. The existing workflow of static provisioning or dynamic provisioning should stay unchanged and all what required is to create a new storage class that use CSI driver as provisioner. The following is an example storageclass manifest file:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: csi-sc
provisioner: ebs.csi.aws.com
```

To pair with current in-tree EBS volume plugin, all the storageclass parameters are supported. The parameters include `type`, `fsType`, `iopsPerGB`, `encrypted` and `kmsKeyId`. `zone/zones` is not included because it is deprecated in kubernetes v1.12 in favor of `allowedTopologies`. And if you are building multi-zone application that requires provisioning volume in different availability zones for Pod to access, volume scheduling could be turned on by setting `volumeBindingMode` to `WaitForFirstConsumer` .

# How to Use it?
In order to use EBS CSI driver, in high level, there are several steps you need to follow:
1. Deploy the driver - this includes creating several service account and cluster roles and deploying EBS CSI driver along with several side car containers such as external provisioner, external attacher and driver registrar. 
1. Create storageclass with the sample manifest file the like above one.
1. Create Persistence Volume Claim (PVC) which references the created storageclass. And a new Persistence Volume (PV) will be created. Once created, it will be bound by the PVC.
1. Deploy the Pod that uses the created PVC.

To simplify this, we created sample manifest files for you to try it out at [installation](https://github.com/kubernetes-sigs/aws-ebs-csi-driver#installation) page.

For more details, please refer to [README](https://github.com/kubernetes-sigs/aws-ebs-csi-driver) on our github page.

# Future Works
We are planning to harden the driver in next kubernetes release cycle 2019 Q1 and have a beta release by then. 

For our future milestones, please refer to [milestone](https://github.com/kubernetes-sigs/aws-ebs-csi-driver/milestones). If you encounter any issues, please report it under [issues](https://github.com/kubernetes-sigs/aws-ebs-csi-driver/issues). And contributions are very welcomed, feel free to contact us if you are interested.

Stay in tuned :)

Thanks,
