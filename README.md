# K3s with Vagrant

![k3s](https://user-images.githubusercontent.com/319498/101405294-c5a14b80-38cf-11eb-8ef9-3edf19dad89d.gif)


## Getting Started

```
# Boot a bare virtual machine and install k3s
➜  vagrant up

# To allow your host kubectl to speak to the cluster
➜  export KUBECONFIG=.kube/config

➜  kubectl get nodes
NAME        STATUS   ROLES    AGE   VERSION
debian-10   Ready    master   26s   v1.19.4+k3s1
```

## Goal

A single portable Vagrantfile with a minimal install of K3s while also maintaining ease of talking to the cluster from a host

## Adding to a new project

```
wget https://raw.githubusercontent.com/gsdevme/k3s-dev/master/Vagrantfile
mkdir .kube/
```

## Usage with Gitops

Manifests can then be deployed via the normal route or via something like https://toolkit.fluxcd.io/get-started/

```
flux check

flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=$GITHUB_REPO \
  --branch=master \
  --path="cluster/dev" \
  --personal
```
