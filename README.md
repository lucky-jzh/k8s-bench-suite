# k8s-bench-suite
Bash scripts collection to benchmark kubernetes cluster performance

## [knb](knb) : Kubernetes Network Benchmark

[knb](knb) is a bash script that will start a networking benchmark on a target Kubernetes cluster.

Here are some highlights:

- **Plain bash script** with very few dependencies
- Complete benchmark **takes only 2 minutes**
- Testing **both TCP and UDP** bandwidth
- **Automatic detection of CNI MTU**
- **Includes host cpu and ram monitoring** in benchmark report
- No ssh access, just an access to the target cluster through standard kubectl
- **No need for high privileges**, the script will just launch very lightweight pods on two nodes.
- Based on very lights containers images :
  - ![Docker Image Size (tag)](https://img.shields.io/docker/image-size/infrabuilder/bench-iperf3/latest) [infrabuilder/bench-iperf3](https://hub.docker.com/r/infrabuilder/bench-iperf3), is used to run benchmark tests
  - ![Docker Image Size (tag)](https://img.shields.io/docker/image-size/infrabuilder/bench-custom-monitor/latest) [infrabuilder/bench-custom-monitor](https://hub.docker.com/r/infrabuilder/bench-custom-monitor), is used to monitor nodes

### Requirements 

This script needs a valid `kubectl` setup with an access to the target cluster.

Binaries dependencies for the host that will execute [knb](knb) :

- awk
- grep
- tail
- date
- kubectl

### Quickstart

Choose two nodes to act as server/client on your cluster (for example node1 and node2) . Then start the knb : 

```bash
./knb --verbose -client-node node1 -server-node node2
```

If you omit the `--verbose` flag, it will also complete, but you will have no output until the end of the benchmark.

### Usage

To display usage, use the `-h` flag :

```bash
aducastel@infrabuilder:~/k8s-bench-suite$ ./knb -h

knb is a network benchmark tool for Kubernetes CNI

There are two modes :
- benchmark mode : will actually run benchmark on a cluster
- from data mode : read data generated by previous benchmark with "-o data" flag

=====[ Benchmark mode ]====================================================

 Mandatory flags :
    -cn <nodename>
    --client-node <nodename>    : Define kubernetes node name that will host the client part
    -sn <nodename>
    --server-node <nodename>    : Define kubernetes node name that will host the server part

 Optionnal flags :
    -d <time-in-scd>
    --duration <time-in-scd>    : Set the benchmark duration for each test in seconds. (Default 10)
    -k
    --keep                      : Keep data directory instead of cleaning it (contains raw benchmark data)
    -n <namespace>
    --namespace <namespace>     : Set the target kubernetes namespace
    -sbs <size>
    --socket-buffer-size <size> : Set the socket buffer size (Default: 256K)
    -t <time-in-scd>
    --timeout <time-in-scd>     : Set the pod ready wait timeout in seconds. (Default 30)

=====[ From Data mode ]====================================================

Mandatory flags :
    -fd <path>
    --from-data <path>          : Define the path to the data to read from

=====[ Common optionnal flags ]============================================

    --debug                     : Set the debug level to "debug"
    --debug-level <level>       : Set the debug level (values: standard, warn, info, debug)
    -h
    --help                      : Display this help message
    -f <filepath>
    --file <filepath>           : Set the output file
    -o <format>
    --output <format>           : Set the output format (values: text, yaml, json, data)
    -v
    --verbose                   : Activate the verbose mode by setting debug-level to 'info'
    -V
    --version                   : Show current script version

=====[ Examples ]==========================================================

  Simple benchmark from "node1" to "node2" in verbose mode
  -------------------------------------------------------------------------
  | knb -v -cn node1 -sn node2                                            |
  -------------------------------------------------------------------------

  Benchmark from "nA" to "nB" with data saved in file "mybench.knbdata"
  -------------------------------------------------------------------------
  | knb -cn nA -sn nB -o data -f mybench.knbdata                          |
  -------------------------------------------------------------------------

  Generate report in json from previous benchmark file "mybench.knbdata"
  -------------------------------------------------------------------------
  | knb -fd mybench.knbdata -o json                                       |
  -------------------------------------------------------------------------
```

### More examples

To run benchmark from node A to node B, showing only result in yaml format :

```bash
./knb -cn A -sn B -o yaml
```

To run benchmark from node Asterix to node Obelix, with the most verbose output and a result as json in a `res.json` file :

```bash
./knb --debug -cn Asterix -sn Obelix -o json > res.json
```

Running benchmark in namespace "myns" :

```bash
./knb -n myns -cn node1 -sn node2
```

