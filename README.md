# K3s Cluster

## K3s cluster, one master and two workers, latest helm is installed, latest stable repo is installed. 


1.  The cluster is based on Ubuntu 18.04.
2.  Each node runs via 1GB of memory only! 
3.  The cluster forms from 3 nodes; one master and two workers (worker1 and worker2). We can add as many as we want of nodes.
4.  The provisioning install latest version of HELM.
5.  The provisioning installs the latest STABLE helm repo.
6.  `kubectl auto-compeltion` is configured for user vagrant.
7.  No need to put `k3s` in-front of `kubectl` command.
