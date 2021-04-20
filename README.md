# SOS-CLUSTER
Link e guide per installare un Bare Metal Cluster Kubernetes con loadbalancing e HA storage.

#### In progress
- HA storage

#### To do
- Load balancing dei worker (Metallb)
- Certificate issuer (Cert-manager)
- CI/CD 

## Juju

Generare chiave ssh sul primo nodo:

```console
ssh-keygen
```

Copiare la chiave ssh su tutti gli altri nodi:

```console
ssh copy-id node2@192.168.1.2
ssh copy-id node3@192.168.1.3
ssh copy-id node4@192.168.1.4
ssh copy-id node5@192.168.1.5
ssh copy-id node6@192.168.1.6
ssh copy-id node7@192.168.1.7
ssh copy-id node8@192.168.1.8
ssh copy-id node9@192.168.1.9
```

Installare juju:

```console
sudo snap install juju --classic
```

Creare il cloud:

```console
juju add-cloud
```

Fare il bootstrap del controller:

```console
juju bootstrap cloud_name manual_controller_name
```

Aggiungere le macchine al cloud creato:

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

## Deploy Charmed Kubernetes

#### Vanilla Bundle

Fare il deploy di Charmed Kubernetes sul nodo dove è installato juju:

```console
juju deploy charmed-kubernetes --map-machines=existing
```

#### Custom bundle

Scaricare il modello custom sul nodo dove è installato juju e fare il deploy:

```console
wget https://raw.githubusercontent.com/lascuolaopensource/pala-cluster/main/charmed-k8s-9nodi-180421.yaml
juju deploy ./*.yaml --map-machines=existing
```

### Scalare master e kubeapi loadbalancer (solo per Vanilla Bundle)

```console
juju add-unit -n 1 kubernetes-master --to 4
juju add-unit -n 2 kubeapi-load-balancer --to 4,5
```

### Deploy HAcluster (solo per Vanilla Bundle)

```console
juju deploy hacluster --series focal
juju config kubeapi-load-balancer ha-cluster-vip="192.168.1.10 192.168.1.11"
juju relate kubeapi-load-balancer hacluster
```

## Teardown
Rimuovere cloud, modelli e controller (sperimentale)

```console
juju destroy-model "MODEL_NAME"
juju destroy-contoller "CONTROLLER_NAME"
juju remove-cloud "CLOUD_NAME"
sudo /sbin/remove-juju-services
rm ~/.kube/config
rm /home/ubuntu/*
```

## Links

[GUI Per Juju](https://jujucharms.com/new)

[Vault come EasyRSA](https://ubuntu.com/kubernetes/docs/using-vault)


### Loadbalancer

[Riflessioni su HA](https://ubuntu.com/kubernetes/docs/high-availability)

[HACluster](https://ubuntu.com/kubernetes/docs/hacluster)

[MetalLB](https://ubuntu.com/kubernetes/docs/metallb)


### Storage

[](https://softwareengineeringdaily.com/2019/01/11/why-is-storage-on-kubernetes-is-so-hard/)

#### Rook-Ceph

[Rook site](rook.io)

[Rook + Ceph - Survival guide](https://www.cloudops.com/blog/the-ultimate-rook-and-ceph-survival-guide/)

[CephFS](https://ubuntu.com/kubernetes/docs/storage)

#### NFS

[Hosted NFS](https://raymondc.net/2018/12/07/kubernetes-hosted-nfs-client.html)

[NFS example](https://github.com/kubernetes/examples/tree/master/staging/volumes/nfs)

#### StorageOS

[StorageOS su Juju](https://juju.is/tutorials/deploying-storageos-on-kubernetes#1-overview)

[StorageOS Host Managing](https://docs.storageos.com/docs/operations/managing-host-storage)

[Possibile Guida StorageOS](https://laptrinhx.com/news/storageos-create-persistent-storage-in-your-charmed-kubernetes-cluster-rxzDQkO)

#### Juju Storage

[Juju Storage Pools and Kubernetes](https://discourse.charmhub.io/t/persistent-storage-and-kubernetes/1078)

[Juju Storage Read Write Many](https://discourse.charmhub.io/t/upcoming-in-2-8-support-for-storage-on-deployments-and-daemonsets/2860)
