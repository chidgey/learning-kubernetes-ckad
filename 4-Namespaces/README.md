# Namespaces
Namespaces play an important role in many tasks when keeping objects organized within the cluster. Namespaces are in effect virtual clusters backed by the same physical cluster and can be used to group objects by project, app, team etc...

If no namespace is specified, the cluster will assume it should be placed in the default namespace.

Use labels to distinguish different resources within the same namespace, for example in a rolling blue-green deployment scenario.

Typically we don't meddle with namespaces until the team / project is complex enough to warrant it.

- [https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)

Simply create an object and set the namespace key value, e.g.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
    namespace: my-ns
    labels:
        app: myapp
spec:
    containers:
    - name: myapp-container
      image: busybox
      command: ['sh', '-c', 'echo Hello K8s! && sleep 3600"]
```

Namespaces can be used for isolating applications, or for isolating teams within a kubernetes cluster. Then through the use of RBAC, control the ability to administer objects within that namespace.

```bash
# List existing namespaces
kubectl get namespaces

# Create a new namespace
kubectl create ns my-ns

# This only searches the default namespace, so if we are using namespaces things won't show up
kubectl get pods

# Nor are you able to get the pod definition this way either
kubectl describe pod my-ns-pod

# If you want to get objects within a namespace, you need to scope the query to use that namespace, e.g.
kubectl get pods -n my-ns
```

**It's important to be looking in the correct namespace when working with your objects in the cluster.**

## Setting the namespace preference
If you want to setup your preference and be done with it, use the following:

```bash
# Set the default context to be something other than default.
kubectl config set-context $(kubectl config current-context) --namespace=<insert-namespace-name-here>

# And validate the result with the following
kubectl config view | grep namespace:
```

## Not all objects are in a namespace
Be warned, some low level objects don't exist in a namespace. You can find out what is in and outside of namespaces with the following commands.

```bash
# In a namespace
kubectl api-resources --namespaced=true

# Not in a namespace
kubectl api-resources --namespaced=false
```