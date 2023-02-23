# Deploy a Production Ready Kubernetes Cluster

![Kubernetes Logo](https://raw.githubusercontent.com/kubernetes-sigs/kubespray/master/docs/img/kubernetes-logo.png)

## Quick Start

Below are several ways to use Kubespray to deploy a Kubernetes cluster.

### Ansible

### Make sure having keys ready:

brew install hudochenkov/sshpass/sshpass

- name: generate SSH key
  hosts: 127.0.0.1
  connection: local

  vars:
    ssh_key_filename: id_rsa_ansible

  vars_prompt:
    - name: "ssh_passphrase"
      prompt: "Enter the passphrase for the SSH key"

  tasks:

    - name: generate SSH key "{{ssh_key_filename}}"
      user:
        name: <ansible_user>
        generate_ssh_key: yes
        ssh_key_type: rsa
        ssh_key_bits: 4096
        ssh_key_file: .ssh/{{ssh_key_filename}}
        ssh_key_passphrase: "{{ssh_passphrase}}"
        force: no
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
Alternative 1: ansible-playbook -i inventory/kubecluster/hosts.yml --become --become-user=root cluster.yml --private-key /Users/<user>/.ssh/id_rsa_ansible -kK

# check Kubernetes cluster
kubectl get nodes --kubeconfig=/etc/kubernetes/admin.conf

# copy the kubeconfig file
scp masternode:/etc/kubernetes/admin.conf ~/.kube/config


ERROR! no action detected in task. This often indicates a misspelled module name, or incorrect module path.
```
This likely indicates that a task depends on a module present in ``requirements.txt``.


