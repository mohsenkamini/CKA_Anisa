# CKA_Anisa
CKA course attended in Anisa


### Components

![image](https://user-images.githubusercontent.com/77579794/236401310-f46b692e-2039-4fb4-acb2-bc68ed960ba1.png)


- **kubelet:**

1. interacts with the container runtime to deploy containers on the node.
2. reports status of the node and the containers on that node to the API server.
3. register nodes on the cluster

- **Controller manager:**

1. monitors the nodes and containers

- **API server:**

1. everyone talk to API server with anything they want to do or report

- **ETCD:**

1. Key-value DB that contains cluster/applications info.

![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/c5a3c575-a347-4191-93fb-1b4933f449bd)

‍‍‍`/etc/kubernetes/manifests/etcd.yml`


- **kube-scheduler:**

1. schedules containers/pods on the nodes.

- **kubeproxy:**

1. between pods/hosts network management


### Container runtime engine

`nerdctl` and `cri` are utilities to manage containerd

`runc` creates namespaces and cgroups

### /etc/kubernetes

`*.conf` files contain the access info on different roles like admin, kubelet, etc.

### $HOME/.kube/config

We can add multiple clusters' info on this file. and using `kubectx` or `kubeconf` switch between users/clusters.

### CNI 
works as net interfaces for containers.
calico and flannel and ... work as the overlay network assigning IPs to those containers.

### kubectl

~~~
kubectl get node
kubectl get pod --namespace kube-system -o wide
kubectl get node --kubeconfig
kubeadm token list
kubeadm token create --print-join-command --ttl=12h
kubectl label node worker1 kubernetes.io/role=worker1
~~~


### nerdctl 

~~~
nerdctl -n k8s.io ps
nerdctl -n k8s.io images
~~~
