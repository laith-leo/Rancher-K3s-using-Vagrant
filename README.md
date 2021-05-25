# HA K3s Cluster

## [K3s](https://k3s.io/) cluster, one master and two workers based on Rancher K3s and Containerd, latest helm is installed, latest [stable](https://github.com/helm/charts/tree/master/stable) repo is installed. 


### Prerequisites::
#### Vagrant and VirtualBox installed on the the host machine. 

### Scenario:
1.  The cluster is based on Ubuntu 20.04.
3.  The cluster forms from 3 nodes; one master and two workers (worker1 and worker2). We can add as many as we want of nodes.
4.  The provisioning install latest version of HELM.
5.  The provisioning installs the latest STABLE helm repo.
6.  `kubectl auto-compeltion` is configured for user vagrant.
7.  No need to put `k3s` in-front of `kubectl` command.
8.  Master node IP Address (host-only) is 192.168.100.100
9.  Worker nodes IP Address (host-only) are 192.168.100.101, 192.168.100.102 and 192.168.100.103.
10. Metallb load balancer is preinstalled.
11. local-path is the default storage class for Kubernetes.
