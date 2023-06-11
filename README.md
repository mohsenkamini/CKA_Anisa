# CKA_Anisa
CKA course attended in Anisa

to initiate a cluster check out [this repo](https://github.com/mohsenkamini/Getting-started-w-Kubernetes).

### Components

![image](https://user-images.githubusercontent.com/77579794/236401310-f46b692e-2039-4fb4-acb2-bc68ed960ba1.png)


- **kubelet:**

1. interacts with the container runtime to deploy containers on the node.
2. reports status of the node and the containers on that node to the API server.
3. register nodes on the cluster

~~~
systemctl restart kubelet
~~~

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
2. creates service endpoints

![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/63c6e88c-676c-44d0-9d9c-d2b7325c9bd4)



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
kubectl get all -A
kubectl get events
~~~

~~~
kubectl run nginx_name --image nginx:latest
kubectl get pod -o yaml
kubectl explain deployments.kind
~~~


### nerdctl 

install:
~~~
NERDCTL_VERSION=1.0.0 # see https://github.com/containerd/nerdctl/releases for the latest release
archType="amd64"
if test "$(uname -m)" = "aarch64"; then     archType="arm64"; fi
wget "https://github.com/containerd/nerdctl/releases/download/v${NERDCTL_VERSION}/nerdctl-full-${NERDCTL_VERSION}-linux-${archType}.tar.gz" -O /tmp/nerdctl.tar.gz
mkdir -p ~/.local/bin
tar -C ~/.local/bin/ -xzf /tmp/nerdctl.tar.gz --strip-components 1 bin/nerdctl
echo -e '\nexport PATH="${PATH}:~/.local/bin"' >> ~/.bashrc
export PATH="${PATH}:~/.local/bin"
nerdctl
~~~

~~~
nerdctl -n k8s.io ps
nerdctl -n k8s.io images
nerdctl -n k8s.io kill 84a0b9e88743
~~~

### PODs

can have multiple containers in it(helper container):

Helper containers can be used to perform a wide range of tasks, such as:

1. Preparing the environment for the main container(s) by downloading configuration files, secrets, and other data.
2. Managing the lifecycle of the main container(s) by monitoring their health and restarting them if necessary.
3. Collecting and sending logs, metrics, and other data to external systems.
4. Running tools and utilities for debugging, profiling, or testing the main container(s).

~~~
kubectl  -n calico-system edit pod  calico-node-rrpxr
~~~

![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/6251d15e-7716-4df6-bd1d-c688bf578b7f)
pod itself does not have control over its state, e.g if the node fails pod won't be relocated.

### replicaset

![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/e9a53699-4edc-4ef7-a131-fdeb71435887)
![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/f16c0102-6380-437d-8db5-ef1fbebe9aa0)
![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/c2f43f23-8e52-4913-bc3a-c45367584114)

~~~
kubectl get replicaset
kubectl get rs -o wide
kubectl replace -f rs-definition.yml
kubectl scale --replicas=3 -f rs-definition.yml
kubectl apply -f rs-definition.yml # no need of a kubectl replace later, do apply again
~~~
#### selector
if we already have a pod with lable type: front-end that pod will be a part of this rs.

### deployment
A ReplicaSet ensures that a specified number of pod replicas are running at any given time. However, a Deployment is a higher-level concept that manages ReplicaSets and provides declarative updates to Pods along with a lot of other useful features like
rolling update/roll back options.

![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/b1dcacc9-9566-4931-b4fc-62892491404d)

### namespace
only pods on the same ns see each other by default.
we can define resource limitations and accesses using ns.
~~~
kubectl create ns dev
~~~
the kube-public namespace exposes its pods to everyone.

- change default ns:
~~~
kubectl config set-context kubernetes-admin@kubernetes --namespace dev
~~~

### ResourceQuota
set requested/limits to resources/object(pods/deployments,etc) for a namespace,lable or etc...

~~~
kubectl get resourcequotas
~~~

### Services
expose pods to outside/other pods within the cluster.
load balance traffic to pods.

![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/75950a77-f430-4236-9325-c9b52a19314d)

node port: only exposes on the host and the endpoint is each host's ip.
![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/1fbf3f43-584c-4341-b45c-639b01c72d54)

![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/f71fe450-4df6-400b-995b-c114b9925d23)
~~~
kubectl get ep  nginx-service -o wide
kubectl get svc nginx-service -o wide
iptables-save | grep 10.104.199.182 # svc ip
~~~

clusterIP: exposes pod in cluster only.
access pods between two namespaces:
~~~
curl <svc-name>.<ns-name>.svc.cluster.local
~~~

LoadBalancer: 

### scheduling
manual through pod definition:
![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/2896f50b-9841-48e0-87b2-23fa07c6c4a8)

label & selectors can be used to do this.

#### annotations

### taints and tolerations

![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/ce12efb9-7637-4533-a03a-f09f34f7dfd5)

if any pod can tolerate blue taint will be deployed on that node. other than that no node will be deployed on that node.

types of taints:
![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/051851c6-9ad0-490e-b725-053a0ee3ae38)

~~~
kubectl taint nodes <node-name> app=blue:NoSchedule
~~~
toleration configuration:
![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/b18b28ea-ffea-4114-baa9-2418f47e58b3)

remove taint:
~~~
kubectl taint node worker01 app=nginx:NoSchedule-
~~~


### Node selector

![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/68aedeaf-ba12-474d-91e6-5eadd80a1e04)

~~~
kubectl label nodes worker01 size=Large

~~~

### Node Affinity
Affinity meaning:
![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/ee7fa937-9ec1-4d8a-9599-52ac36dc608d)


define more detailed conditions/predictions than node selector.

![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/cf599bde-f1b0-46b7-a456-81db36a30705)


### Resource limit exceeding

CPU: K8s throttles(in queues) the cpu for the pod
Memory: K8s terminate the pod (OOM(Out of Memory) killed)
 
### Daemonset
just like the swarm service on global mode. 1 container/pod on each node.
kube-proxy is deployed in this way.
~~~
kubectl -n kube-system get ds
~~~

### StaticPod
The initial pods like etcd come up before the apiServer, so some pods get initialized by kubelet directly and not handled by the apiServer.
![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/9abcf12a-3bd3-4690-93e4-cc11c6b90390)

**kubelet is always watching the pod manifest directory to understand what pods to run.**

![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/f083387d-6f30-4384-965f-1a1221286982)


### statefulset

### job

### cronjob




### calico

Tshoot:
~~~
curl -L https://github.com/projectcalico/calico/releases/latest/download/calicoctl-linux-amd64 -o /usr/bin/calicoctl
chmod +x /usr/bin/calicoctl
calicoctl node status
~~~

### create a pod using yaml file
how to get apiVersion, kind , etc(differs for each object/manifest (pod, etc)): 

core group is the default and does not need to be mentioned in yaml.

https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#pod-v1-core
![image](https://github.com/mohsenkamini/CKA_Anisa/assets/77579794/ce69cf14-3656-41aa-9509-f6fed9424b21)

these are the four necessary keys for pods:

~~~
apiVersion: v1
kind: Pod   # always starts with capital letter
metadata:   # name of the pod, namespace, labels etc
  name: myapp-pod
  labels:
    app: myapp
    type: front-end

spec:
  containers:
    - name: nginx-container
      image: nginx:latest # default pulls out of dockerhub
~~~


