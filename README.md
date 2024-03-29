# Kubernetes cluster using Vagrant and Ansible

## Objective

This repository describes the steps required to setup a multi node Kubernetes cluster for development purposes. This setup provides a production-like cluster that can be setup on your local machine.

## Why do we require multi node cluster setup?

Multi node Kubernetes clusters offer a production-like environment which has various advantages. Even though Minikube provides an excellent platform for getting started, it doesn’t provide the opportunity to work with multi node clusters which can help solve problems or bugs that are related to application design and architecture. For instance, Ops can reproduce an issue in a multi node cluster environment, Testers can deploy multiple versions of an application for executing test cases and verifying changes. These benefits enable teams to resolve issues faster which make the more agile.

## Why use Vagrant and Ansible?

Vagrant is a tool that will allow us to create a virtual environment easily and it eliminates pitfalls that cause the works-on-my-machine phenomenon. It can be used with multiple providers such as Oracle VirtualBox, VMware, Docker, and so on. It allows us to create a disposable environment by making use of configuration files.

Ansible is an infrastructure automation engine that automates software configuration management. It is agentless and allows us to use SSH keys for connecting to remote machines. Ansible playbooks are written in yaml and offer inventory management in simple text files.

## Prerequisites

- Vagrant should be installed on your machine. Installation binaries can be found [here](https://www.vagrantup.com/downloads).
Oracle VirtualBox can be used as a Vagrant provider or make use of similar providers as described in Vagrant’s official documentation.
- Ansible should be installed in your machine. Refer to the Ansible installation guide for platform specific installation.

# Bring the cluster up

      $ git clone https://gitlab.com/arkhoss/kube-cluster-vagrant.git && cd kube-cluster-vagrant

      $ vagrant up

# Access the master

      $ vagrant ssh k8s-master
      vagrant@k8s-master:~$ kubectl get nodes
      NAME         STATUS   ROLES    AGE     VERSION
      k8s-master   Ready    master   18m     v1.13.3
      node-1       Ready    <none>   12m     v1.13.3
      node-2       Ready    <none>   6m22s   v1.13.3

# Access the nodes

      $ vagrant ssh node-1

# Notes

In this setup we use custom CIDR for pod cni `192.168.61.0/24` If you want to change the network range for pods, change `ansible/files/calico.yaml`

```yaml
            - name: CALICO_IPV4POOL_CIDR
              value: "192.168.61.0/24"
```
and also update `ansible/playbooks/master-playbook.yml`

```yaml
      - name: Initialize the Kubernetes cluster using kubeadm
        register: result
        ansible.builtin.shell: >
          kubeadm init --apiserver-advertise-address={{ node_ip }} --apiserver-cert-extra-sans={{ node_ip }}  --node-name k8s-master --pod-network-cidr="192.168.61.0/24" # <<< change this range
```
