---
title: nothing
tags:
  - kubernetes
  - proxmox
  - homelab
  - bash
  - k8
  - linux
date: 2025-09-04T22:37:00
published: false
slug: testpage
description: is officia ut t culpa culpa est. Proident exercitation eiusmod eu labo
---

# Set-up Environment

  

>**step I**

- [ ] create `controller` and `node` vm's

  

>**step II**

- [ ] Apply static IP

>back-up before edit - ==skip if container==

```sh

sudo nvim /etc/netplan/50-cloud-init.yaml

```

>modify config

```yaml

network:

 version: 2

 ethernets:

   ens18:

         addresses: [192.168.0.32/24] # vm ip

         nameservers:

             addresses: [1.1.1.1] # dns

         routes:

             - to: default

               via: 192.168.0.1 # default gateway

```

>test connection

```sh

sudo netplan try

```

  

>**step IV**

- [ ] Install Container runtime

```sh

sudo apt install containerd -y

```

- [ ] create containerd folder

```sh

sudo mkdir /etc/containerd

```

- [ ] create default config

```sh

containerd config default | sudo tee /etc/containerd/config.toml

```

- [ ] set `/etc/containerd/config.toml` > `SystemdCgropup = true`

  

>**step V**

- [ ] disable swap

> inspect memory

```sh

free -m

```

>disable if exists | `/etc/fstab`

```sh

# /swap.img .... comment out

```

  

>step VI | configs

- [ ] `/etc/sysctl.conf` > enable `net.ipv4.ip_forward=1` forwarding

- [ ] enable bridging `/etc/modules-load.d/k8s.conf`

```conf

# ensure bridging support

br_netfilter

```

  

---

# Setup Kubernetes

  

>**step I**

- [ ] install gpg keys

```sh

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

```

>step II

- [ ] install reference repo - ubuntu server

```sh

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

```

>step III

- [ ] install dependencies | [install - guide](https://v1-32.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

```sh

sudo apt install kubeadm kubectl kubelet

# kubeadm - helps bootstrap cluster

# kubectl - cli tool:the component that runs on all of the machines in your cluster and does things like starting pods and containers

# kubelet - the command line util to talk to your cluster

```