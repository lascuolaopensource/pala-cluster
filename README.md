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
juju bootstrap sos-cloud manual-controller
```

Aggiungere le macchine:

```console
juju add-machine ssh:user1@192.168.1.1
juju add-machine ssh:user2@192.168.1.2
juju add-machine ssh:user2@192.168.1.3
juju add-machine ssh:user3@192.168.1.4
juju add-machine ssh:user4@192.168.1.5
juju add-machine ssh:user5@192.168.1.6
juju add-machine ssh:user6@192.168.1.7
```

# Deploy Charmed Kubernetes
Deployare Kubernetes con il file copiato:

```console
juju deploy ./*.yaml --map-machines=existing
```

# Remove Juju
Rimuovere cloud e controller

```console
juju destroy-model "MODEL_NAME"
juju destroy-contoller "CONTROLLER_NAME"
sudo /sbin/remove-juju-services
rm ~/.kube/config
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
