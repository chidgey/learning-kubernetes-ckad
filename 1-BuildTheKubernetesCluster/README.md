# Build the Kubernetes Cluster
Our first step is to spin up a few virtual machines so we can play around with a new Kubernetes cluster.

The configuration is as follows:

x1 Master node (Kube Master)
- achidgey1c.mylabserver.com

x2 Worker nodes (Kube Worker 1 & 2)
- achidgey2c.mylabserver.com
- achidgey3c.mylabserver.com

## Next, prepare all instances in the following way
We need to configure signing keys and repos, for installing tooling onto the cluster.

```bash
# Docker repo signing key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Docker repo
sudo add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

# K8s repo signing key
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

# Setup apt-get sources
cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

# Update packages
sudo apt-get update

# Install K8s, specific version due to compatibility for the follow along tutorial,
# note that if you bump the version numbers, they must all match.
sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu kubelet=1.13.5-00 kubeadm=1.13.5-00 kubectl=1.13.5-00

# Freeze our docker and k8s package versions, we don't want apt-get update to cause havoc later.
sudo apt-mark hold docker-ce kubelet kubeadm kubectl

# Setup networking for k8s to function
echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf

# Apply the above change immediately
sudo sysctl -p
```

## On the master node, apply the following
```bash
# Initialize the cluster
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

# Setup the local kube configuration file
mkdir -p $HOME/.kube

# Take a copy of the admin conf
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

# Set config permissions
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Next we need to setup flannel networking, briefly put: flannel is a virtual network that gives a subnet to each host for use with container runtimes. 

Platforms like Google's Kubernetes assume that each container (pod) has a unique, routable IP inside the cluster. The advantage of this model is that it reduces the complexity of doing port mapping.
[Read more about them at CoreOS](https://coreos.com/flannel/docs/latest/)

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```

You can see what the YAML declaration file above contains [Here](kube-flannel.yml)

## Now join both worker nodes to the cluser

Please note where the $controller_private_ip, $token and $hash values are defined after configuring the master node with: sudo kubeadm init --pod-network-cidr=10.244.0.0/16 earlier. There should be a line at the end of the installation.
```bash
sudo kubeadm join $controller_private_ip:6443 --token $token --discovery-token-ca-cert-hash $hash
```

It should look something like:

```bash
sudo kubeadm join 172.31.22.141:6443 --token ljhbmr.hbzl22n6d7ls15tj --discovery-token-ca-cert-hash sha256:eb3dca6e7a71024c8d8123a071b5e43c3a433633f2d412101b6fb61d77dc986d
```

## Test our nodes are connected to the cluster
On the master node, type:
```bash
kubectl get nodes
```

And you should see something similar to the following:
```text
NAME                         STATUS   ROLES    AGE     VERSION
achidgey1c.mylabserver.com   Ready    master   33m     v1.13.5
achidgey2c.mylabserver.com   Ready    <none>   8m51s   v1.13.5
achidgey3c.mylabserver.com   Ready    <none>   8m43s   v1.13.5
```

Awesome, we know have a cluster for playing around with.