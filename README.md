# c4-bench: Cloud Container Cluster Common Benchmark

The engineering community at large seems enamored with containers and container platforms like Swarm, Kubernetes, and Mesos. These force multiplying tools are helping the whole community push technological progress faster than ever before.

But there is a difference between having a tool and understanding that tool's strengths and weaknesses relative to the greater ecosystem. It is difficult to improve any tool that you have not measured. Open and repeatable benchmarks, approachable analysis, and clear specific criteria for evaluation will help decision makers do so from an informed and data-driven context.

Please help us further the conversation. We need data on other stacks, cloud providers, and cluster configurations. We need an expanded common benchmark that covers more features. We need a more extendable test harness. We need more people interested in taking the conjecture out of decision making.

Please run these tests yourself and make or suggest improvements. If you're inspired and build something on your own don't hesitate to share it with the world. We need the data.

## Building Clusters

See the README in the target platform directory assembly instructions.

## Running the Benchmark

Each cluster will start a machine from which the tests should be run. This machine has a known IP address and tests must always be run from that IP address. Once the AWS CF stack has been created access the TestInstance over SSH at the public IP provided in the stack outputs.

From a shell on the test instance verify that Docker is up and running:

    docker -H tcp://localhost:2376 info

Verify that the benchmark image has been built:

    docker -H tcp://localhost:2376 images | grep local/bench

Before you start the benchmark make sure that the Swarm or Kube cluster is up and healthy:

    # for Swarm
    docker -H tcp://10.0.0.20:3376 info | grep Status | sort | uniq -c

    # for Kube... insert the stack output named, "KubernetesAPIEndpoint" for <APISERVERDNS>
    kubectl --server="http://<APISERVERDNS>:8080" get nodes | wc -l
    # will show 1001 including the header line for a full cluster

Then start the benchmark with the following command:

    docker -H tcp://localhost:2376 run -d --name bench --net host -v /results:/results -w /results local/bench

Test results will be written to appropriately named files in /results. WHen you're done, tar them up and use SCP to get them off the box.

You can monitor the test progress by running:

    docker -H tcp://localhost:2376 logs -tf bench

I add timestamps with the `-t` flag so I can get an idea how quickly progress is being made or judge if the test has gotten stuck.

## Important IP Addresses in the Clusters

* The test instance is always at 10.0.0.40
* The swarm managers are always at 10.0.0.20
* Primary etcd instance is always at 10.0.0.10
* Optional secondary etcd instance is always at 10.0.0.11
* Nodes are always started in the 10.0.128.0/17 subnet (10.0.0.0/17 conflicts with some LXC config that conflicts with machines at 10.0.3.0/24)
* Kube API servers are always to be accessed by the ELB DNS name


## Copyright Notice

Copyright &copy; 2016 Jeff Nickoloff - All in Geek Consulting Services, LLC

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
