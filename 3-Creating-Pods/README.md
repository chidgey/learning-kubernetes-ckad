# Creating Pods
Pods are central to kubernetes, they are the atomic unit of deployment in how we run containers.

- [https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/)

A pod consists of one or more containers. A pod also has a set of resources shared.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
    labels:
        app: myapp
spec:
    containers:
    - name: myapp-container
      image: busybox
      command: ['sh', '-c', 'echo Hello K8s! && sleep 3600"]
```

Labels are used to keep objects organised, as well as to apply different types of automation based upon types of pods.

All k8s objects require a spec, in our example above we have a single container in a pod based on the busybox image and a simple run command to print out and pause for 10 minutes before exiting.

## Create a pod from a yaml definition file
kubectl create -f my-pod.yml

Now run, kubectl get pods and see the pod is running. Note that this container will only run for 10 minutes before winding down.

## Edit a pod by updating the yaml definition
kubectl apply -f my-pod.yml

## Edit a pod directly in the cluster
kubectl edit pod my-pod

Note that this isn't always possible, but when it is possible and it might be on the exam. It's a very easy way to make changes to the pod whilst running.

## Delete a pod
kubectl delete pod my-pod