# Kuberbetes API Primitives
Primitives are referred to as objects elsewhere in the documentation. These primitives are used to define the way applications are built in kubernetes.

- [https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)

Some examples of the objects avaliable are:

- Pod
- Node
- Service and
- ServiceAccount

For a full list of all the object types, try the following:

```bash
kubectl api-resources -o name
```

All objects have a spec and a status. The **Spec** is provided to the cluster and defines the desired state declaratively whereas the **Status** is given by the kubernetes cluster and contains information about the current state of the object.

You can get more info about an object's spec and status using:

```bash
kubectl describe $object_type $object_name
```

For the CKAD course, everything we do will more or less be working with the API Primitives / Objects, so lets get more familiar with querying these things.

```bash

# No custom pods on a fresh build, but you get the idea
kubctl get pods

# Get all pods relating to the kube-system (internal) namespace
kubectl get pods -n kube-system

# See our worker nodes
kubectl get nodes

# Filter a specific node
kubectl get nodes $node_name

# Dump the node status
kubectl get nodes $node_name -o yaml

# This will spew out a lot of info about the node, it's great for reviewing the status of a node. It's a more user friendly view of the node status given in yaml above. Use this for troubleshooting.
kubectl describe node $node_name
```

**For the exam, use kubectl describe when troubleshooting resources in a cluster.**