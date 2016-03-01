# Building a Kubernetes Batch Cluster

### Tools

* packer
* cloudformation
* docker
* download the kubernetes release version you want to test

## Building Node AMIs

There are five types of nodes in this cluster. Packer templates are located in each of the subdirectories of this folder. Most of those templates require the kublet and kube-proxy binaries to be copied in to the folder prior to building.

Before you can launch the cluster in AWS you'll need to build AMIs for each of the node types. Currently the packer templates are hard coded to use us-west-2 so that is the region where you will need to run your tests.

Once you've got the binaries you'll run `packer build` for each of the *.packer.json files. Make note of the resulting AMI ID and provide those for the CloudFormation parameters when you launch the cluster.

## AWS Limits

These are large clusters and require resources well beyond what AWS typically makes available. In order to launch a 1000 node cluster from this template you will need at least 1000 m3.medium instances, a few m3.xlarge instances, a c4.xlarge instance. This template uses a VPC. So, make sure you have enough available (the default VPC limit is 10). Last, this template adds a 30GiB GP2 SSD volume to each instance. That means that you'll need an increased total volume limit.