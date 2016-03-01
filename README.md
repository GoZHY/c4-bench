# c4-bench: Cloud Container Cluster Common Benchmark


## Building Clusters

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
