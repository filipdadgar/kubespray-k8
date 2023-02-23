# Deploy a Production Ready Kubernetes Cluster

![Kubernetes Logo](https://raw.githubusercontent.com/kubernetes-sigs/kubespray/master/docs/img/kubernetes-logo.png)

## Quick Start

Below are several ways to use Kubespray to deploy a Kubernetes cluster.

### Ansible

#### Usage

Install Ansible according to [Ansible installation guide](/docs/ansible.md#installing-ansible)
then run the following steps:

```ShellSession
# clone the Kubespray repository
git clone https://github.com/kubernetes-incubator/kubespray.git

# install python3-pip and git
sudo apt install python3-pip git -y

# change working directory and install Python
cd kubespray
sudo pip3 install -r requirements.txt

# install Python dependencies
sudo pip3 install -r contrib/inventory_builder/requirements.txt

# copy sample of inventory to kubecluster
cp -rfp inventory/sample inventory/kubecluster

# create variable IPS
declare -a IPS=(192.168.1.9 192.168.1.10 192.168.1.11)

# generating inventory file
CONFIG_FILE=inventory/kubecluster/hosts.yml python3 contrib/inventory_builder/inventory.py ${IPS[@]}

# Edit the inventory file to match your cluster configuration

all:
  hosts:
    master:
      ansible_host: 192.168.1.9
      ip: 192.168.1.9
      access_ip: 192.168.1.9
    node1:
      ansible_host: 192.168.1.10
      ip: 192.168.1.10
      access_ip: 192.168.1.10
    node2:
      ansible_host: 192.168.1.11
      ip: 192.168.1.11
      access_ip: 192.168.1.11
  children:
    kube_control_plane:
      hosts:
        master:
    kube_node:
      hosts:
        node1:
        node2:
    etcd:
      hosts:
        master:
    k8s_cluster:
      children:
        kube_control_plane:
        kube_node:
    calico_rr: #changed to flannel_rr
      hosts: {}

# Review and change parameters under ``inventory/mycluster/group_vars``
cat inventory/mycluster/group_vars/all/all.yml
cat inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml

# deploy Kubernetes Cluster
ansible-playbook -i inventory/kubecluster/hosts.yml --become --become-user=root cluster.yml

# check Kubernetes cluster
kubectl get nodes --kubeconfig=/etc/kubernetes/admin.conf

# copy the kubeconfig file
scp masternode:/etc/kubernetes/admin.conf ~/.kube/config


ERROR! no action detected in task. This often indicates a misspelled module name, or incorrect module path.
```
This likely indicates that a task depends on a module present in ``requirements.txt``.

## Documents

- [Requirements](#requirements)
- [Kubespray vs ...](docs/comparisons.md)
- [Getting started](docs/getting-started.md)
- [Setting up your first cluster](docs/setting-up-your-first-cluster.md)
- [Ansible inventory and tags](docs/ansible.md)
- [Integration with existing ansible repo](docs/integration.md)
- [Deployment data variables](docs/vars.md)
- [DNS stack](docs/dns-stack.md)
- [HA mode](docs/ha-mode.md)
- [Network plugins](#network-plugins)
- [Vagrant install](docs/vagrant.md)
- [Flatcar Container Linux bootstrap](docs/flatcar.md)
- [Fedora CoreOS bootstrap](docs/fcos.md)
- [Debian Jessie setup](docs/debian.md)
- [openSUSE setup](docs/opensuse.md)
- [Downloaded artifacts](docs/downloads.md)
- [Cloud providers](docs/cloud.md)
- [OpenStack](docs/openstack.md)
- [AWS](docs/aws.md)
- [Azure](docs/azure.md)
- [vSphere](docs/vsphere.md)
- [Equinix Metal](docs/equinix-metal.md)
- [Large deployments](docs/large-deployments.md)
- [Adding/replacing a node](docs/nodes.md)
- [Upgrades basics](docs/upgrades.md)
- [Air-Gap installation](docs/offline-environment.md)
- [NTP](docs/ntp.md)
- [Hardening](docs/hardening.md)
- [Mirror](docs/mirror.md)
- [Roadmap](docs/roadmap.md)