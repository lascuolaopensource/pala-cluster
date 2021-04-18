# pala-cluster
Riflessioni e guide per mettere su un cluster Kubernetes con Juju

# Juju

Copiare il file ```*.yaml``` sul primo nodo.

Installare juju:

```console
sudo snap install juju --classic
```

Creare il cloud:

```console
juju add-cloud
```

Bootstrap del controller:

```console
juju bootstrap cloud_name manual_controller_name
```

Aggiungere le macchine:

```console
juju add-machine ssh:node0@192.168.1.1
juju add-machine ssh:node1@192.168.1.2
juju add-machine ssh:node2@192.168.1.3
juju add-machine ssh:node3@192.168.1.4
juju add-machine ssh:node4@192.168.1.5
juju add-machine ssh:node5@192.168.1.6
juju add-machine ssh:node6@192.168.1.7
juju add-machine ssh:node7@192.168.1.8
juju add-machine ssh:node8@192.168.1.9

```

# Deploy Charmed Kubernetes
Deployare Kubernetes con il file copiato:

```console
juju deploy ./*.yaml --map-machines=existing
```

# Scale kubeapi loadbalancer

```console
juju deploy charmed-kubernetes
juju add-unit -n 2 kubeapi-load-balancer --to 4,5
```
# Deploy HAcluster

```console
juju deploy hacluster --series focal
juju config kubeapi-load-balancer ha-cluster-vip="192.168.1.10 192.168.1.11"
juju relate kubeapi-load-balancer hacluster
```

# If everything goes wrong
Rimuovere cloud, model e controller (beta)

```console
juju destroy-model "MODEL_NAME"
juju destroy-contoller "CONTROLLER_NAME"
juju remove-cloud "CLOUD_NAME"
sudo /sbin/remove-juju-services
rm ~/.kube/config
rm /home/ubuntu/*
```

# Links

[GUI Per Juju](https://jujucharms.com/new)

[CephFS](https://ubuntu.com/kubernetes/docs/storage)

[Vault come EasyRSA](https://ubuntu.com/kubernetes/docs/using-vault)

[Riflessioni su HA](https://ubuntu.com/kubernetes/docs/high-availability)

[HACluster](https://ubuntu.com/kubernetes/docs/hacluster)

[MetalLB](https://ubuntu.com/kubernetes/docs/metallb)

[StorageOS su Juju](https://juju.is/tutorials/deploying-storageos-on-kubernetes#1-overview)

[StorageOS Host Managing](https://docs.storageos.com/docs/operations/managing-host-storage)

[Possibile Guida StorageOS](https://laptrinhx.com/news/storageos-create-persistent-storage-in-your-charmed-kubernetes-cluster-rxzDQkO)

[Juju Storage Pools and Kubernetes](https://discourse.charmhub.io/t/persistent-storage-and-kubernetes/1078)

[Juju Storage Read Write Many](https://discourse.charmhub.io/t/upcoming-in-2-8-support-for-storage-on-deployments-and-daemonsets/2860)
