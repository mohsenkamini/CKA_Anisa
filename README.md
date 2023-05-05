# CKA_Anisa
CKA course attended in Anisa


### components

- **kubelet: **

1. interacts with the container runtime to deploy containers on the node.
2. reports status of the node and the containers on that node to the API server.

- ** Controller manager:**

1. monitors the nodes and containers

- **API server:**

1. everyone talk to API server with anything they want to do or report

- **ETCD:**

1. Key-value DB that contains cluster/applications info.

- **kube-scheduler: **

1. schedules containers/pods on the nodes.

- **kubeproxy:**

1. between pods/hosts network management
