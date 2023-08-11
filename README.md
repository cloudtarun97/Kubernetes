# Kubernetes
Prerequisites:  2 or 3 Ubuntu 20.04 LTS System with Minimal Installation Minimum 2 or more CPU, 3 GB RAM. Disable SWAP on All node SSH Access with sudo privileges

# Kubeadm Cluster installation
# K8 t2.small, Minimum 2 or more CPU, 3 GB RAM.

cus tcp,: 8080,30080,8085,8081,6379,9000,30443,6443
http: 80,443
ssh: 22
----------------------------------------
Step #1: IPtables to see bridged traffic

Master and slave 

```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
```

```
sudo modprobe overlay
```

```
 sudo modprobe br_netfilter
```

```
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF
```

```
sudo sysctl --system 
```
------------------------------------------------
Step #2:Disable swap on all the Nodes

``` 
sudo swapoff -a
(crontab -l 2>/dev/null; echo "@reboot /sbin/swapoff -a") | crontab - || true
```
------------------------------------------------
Step #3:Install CRI-O Runtime On All The Nodes

```
cat <<EOF | sudo tee /etc/modules-load.d/crio.conf
overlay
br_netfilter
EOF
```

```
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
```

```
sudo modprobe overlay
sudo modprobe br_netfilter
```

```
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
```

```
sudo sysctl --system
```

----------------------------------------------------------------
Step #4:Install Kubeadm & Kubelet & Kubectl on all Nodes

```
sudo apt-get update

sudo apt-get install -y apt-transport-https curl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

```

(add the below line) 
```
 sudo nano /etc/apt/sources.list.d/kubernetes.list 
	-> deb https://apt.kubernetes.io/ kubernetes-xenial main EOF
```

```
sudo apt-get update
```

```
sudo apt-get install -y kubelet kubeadm kubectl 
```

```
sudo apt-mark hold kubelet kubeadm kubectl 
```

------------------------------

 sudo chmod +x common.sh
./common.sh

Run the ./common.sh file (kubeadm-scripts/scripts) in this location on both master & worker nodes

Now you need to change master.sh file in master node

[ PUBLIC_IP_ACCESS="false"

False replace with true

PUBLIC_IP_ACCESS="true"

Now run the master.sh file  ]

Use the following commands from the output to create the kubeconfig on master node

```
mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown $(id -u):$(id -g) $HOME/.kube/config 
 ```

To join the worker node command into control plane

``` 
kubectl token create --print-join-command
```

(copy the output command and paste over in the worker node)

``` 
kubectl get nodes
 ```

 ```
 kubectl get nodes
 ```

Step #5:Deploy Sample Nginx microservice on Kubernetes

``` 
kubectl apply -f nginx-deploy.yml
 ```
Now access the nginx service by using worked node IP and port 32000



##############################################################################################
