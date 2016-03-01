# Building a Kubernetes Batch Cluster

### Tools

* packer
* cloudformation
* docker
* download and export quay.io/coreos/etcd:v2.2.1 as etcd.2.2.1.tar

## Building Node AMIs

There are three types of nodes in this cluster. Packer templates are located in each of the subdirectories of this folder. The only one with a dependency you must provide is etcd-node.

Before you can launch the cluster in AWS you'll need to build AMIs for each of the node types. Currently the packer templates are hard coded to use us-west-2 so that is the region where you will need to run your tests.

Once you've got the binaries you'll run `packer build` for each of the *.packer.json files. Make note of the resulting AMI ID and provide those for the CloudFormation parameters when you launch the cluster.

### etcd Node

To get the etcd.2.2.1.tar file run the following locally:

    docker pull quay.io/coreos/etcd:v2.2.1
    docker save -o etcd.2.2.1.tar quay.io/coreos/etcd:v2.2.1

Place the file in the etcd-node directory and the packer template will load it correctly.

## AWS Limits

These are large clusters and require resources well beyond what AWS typically makes available. In order to launch a 1000 node cluster from this template you will need at least 1000 m3.medium instances, a few m3.xlarge instances, a c4.xlarge instance. This template uses a VPC. So, make sure you have enough available (the default VPC limit is 10). Last, this template adds a 30GiB GP2 SSD volume to each instance. That means that you'll need an increased total volume limit.