# k8s-cluster-vagrant-ansible

## Description

This project allows you to deploy a multi-node kubernetes cluster with the help of Vagrantfile and Ansible

## How to

### Deploy and run the nodes

```sh
vagrant up
```

### Configure the k8s cluster

You can configure your k8s cluster by editing the `CONFIGURATION VARIABLES` available in the `Vagrantfile`

### Configure your kubectl

Here's how to configure the kubectl tool on your local machine to communicate with the kubernetes API :

```sh
scp -r vagrant@192.168.50.10:/home/vagrant/.kube $HOME/
password = vagrant
```

Test your config like the example bellow :

```sh
kubectl get nodes
```

Result : 

```
NAME       STATUS   ROLES    AGE   VERSION
master     Ready    master   35m   v1.15.1
worker-1   Ready    <none>   30m   v1.15.1
```

### Connect to the master via ssh

Either you are at the same level as your Vagrantfile, in this case you run the following command :

```sh
vagrant ssh master
```

Either you are in another folder :

```sh
ssh -r vagrant@192.168.50.10
password = vagrant
```
