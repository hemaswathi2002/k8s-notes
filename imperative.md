Deployment - Desired state, reconciles towards replica set, doesn't own pods

k8s
STABLE CLUSTER IP


PORTS:

Client => Service port => Kube-proxy(responsible for network communication) => Pod targetport 

Port - Clients => Service
Target Port => Container Port(listening)
Node Port - 30000–32767

LABEL SELECTOR RULES:

Checks only if labels mentioned in selector, if it matches - includes in endpoint
Selector - No labels mentioned; Pod - Labels Mentioned - It matches the pod

SCENARIO - Service
Assigns the cluster ip from the service
Endpoint Controller watche services with SELECTOR, and search across all pods(within namespace), find pods whose labels match and assigns to it
Kube-proxy watches on every node and watches its endpoints, it programs iptable rules
CoreDns watch service and create a dna A REC. <service-name>.<namespace>.svc.cluster.local → ClusterIP


SCENARIO - POD DIES
Status => Terminating, kubelet =>  SIGTERM, waits terminationGracePeriodSeconds
Endpoint controller removes the podip b4 terminating completely (configurable with preStop hooks)
Kube-proxy removes the iptable rule for pod ip
Replicaset controller ensures desired state = actual state, create a replacement pod if not matching
New podip is assigned to endpoint on the success of readiness check


