---
title: "Quick-Start"
weight: 1
---

There are many ways to run k3s, we cover a couple easy ways to get started in this section.
The [installation options](../installation) section will cover in greater detail how k3s can be setup.

Install Script
--------------
The k3s `install.sh` script provides a convenient way for installing to systemd or openrc,
to install k3s as a service just run:
```bash
curl -sfL https://get.k3s.io | sh -
```

A kubeconfig file is written to `/etc/rancher/k3s/k3s.yaml` and the service is automatically started or restarted.
The install script will install k3s and additional utilities, such as `kubectl`, `crictl`, `k3s-killall.sh`, and `k3s-uninstall.sh`, for example:

```bash
sudo kubectl get nodes
```

`K3S_TOKEN` is created at `/var/lib/rancher/k3s/server/node-token` on your server.
To install on worker nodes we should pass `K3S_URL` along with
`K3S_TOKEN` or `K3S_CLUSTER_SECRET` environment variables, for example:
```bash
curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=XXX sh -
```

> **NOTE:**
Running in a secure environment (INPUT table default policy set to DROP or REJECT), you need to ensure that API server port is accessible from the k3s agent (eg: worker node). In order to do so, add the following rule to iptables:
```bash
iptables -A INPUT -s `K3S_AGENT_IP` -d `K3S_MASTER_IP` -p tcp --dport 6443 -j ACCEPT
```
Where `K3S_AGENT_IP` is the ip address of the worker node and `K3S_MASTER_IP` is the ip address of the master node.

Manual Download
---------------
1. Download `k3s` from latest [release](https://github.com/rancher/k3s/releases/latest), x86_64, armhf, and arm64 are supported.
2. Run server.

```bash
sudo k3s server &
# Kubeconfig is written to /etc/rancher/k3s/k3s.yaml
sudo k3s kubectl get nodes

# On a different node run the below. NODE_TOKEN comes from
# /var/lib/rancher/k3s/server/node-token on your server
sudo k3s agent --server https://myserver:6443 --token ${NODE_TOKEN}
```
