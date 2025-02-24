# System Tuning
This chart helps tune an ACP to be more performant as a compact, hyperconverged-style platform running multiple types of workloads, such as virtual machines, containers, and more.

Since ACPs are typically deployed as "compact clusters", tuning requirements are different than clusters that exist in a datacenter or cloud with many workers and dedicated control plane and worker nodes.

The main element to tune is the number of pods allowed per core and per node, however additional tuning is possible using this chart.

## Node Tuning
The main things to tune on HA/compact and non-HA/single-node ACPs is the number of pods that a node can run per-core and per-node, and the amount of resources reserved by the system for control plane functionality.

These are both housed under the `nodeTuning` key, and broken up under `kubeletConfig` for pods per node/core, and `systemReservedResources` for the amount of CPU and memory reserved for system functions.

In this example, the following is set:
- the number of pods per core is set to `0`, meaning "unlimited"
- the max number of pods per node is set to `1000`
- the amount of CPU reserved for system functions is set to 4000m (4 cores)
- the amount of memory reserved for system functions is set to 4Gi

```yaml
nodeTuning:
  kubeletConfig:
    podsPerCore: 0
    maxPods: 1000
  SystemReservedResources:
    cpu: 4000m
    memory: 4Gi
```

## Jumbo Frames and Cluster Network Tuning
If the network supports running frame sizes above the standard 1500, then two configurations should be made: interface MTUs should be set to the appropriate MTU, and the cluster network should be configured to take advantage of the increased MTU.

These are set under the `networkTuning` key, and are broken up further, where `interfaceMTUs` contains a list of interfaces that should have their MTU set and to what value, and optionally, the priority of applying that change, and the `clusterNetworkMTUs`, which set the machine and network MTUs for the cluster network.

In this example, the following is set:
- interface eno1's MTU is set to 9000, with priority 98
- interface enp4s0f0's MTU is set to 9000, with priority 98
- interface bond0's MTU is set to 9000, with default priority of [99][1]
- the cluster machine MTU is set to 9000, matching the MTU of the underlying cluster interface of bond0
- the cluster network's MTU is taken from 1400 to [8900][2]

[1]: It's recommended to apply MTUs on interfaces within a bond _first_, then apply the MTU to the bond after
[2]: The cluster's MTU is always the machine MTU minus 100, which is needed due to encapsulation overhead

```yaml
networkTuning:
  interfaceMTUs:
    - interface: bond0
      mtu: 9000
    - interface: eno1
      mtu: 9000
      priority: 98
    - interface: enp4s0f0
      mtu: 9000
      priority: 98
  clusterNetworkMTUs:
    mtu:
      machine:
        to: 9000
      network:
        from: 1400
        to: 8900
```

## Driver Tuning
For improved performance of underlying hardware resources, certain drivers can be tuned as well. The ACP will automatically apply these configurations in a staged rollout as needed.

Driver tuning is kept under the `driverTuning` key.

Currently, this chart only supports tuning the `ixgb` driver. Set the value to `true` for tuning to happen.

```yaml
driverTuning:
  ixgb: true
```