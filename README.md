# install_kubernetes


## reset kubernetes
```
kubeadm reset -f
```

## Initializing your master
```
kubeadm init  --pod-network-cidr=192.168.0.0/16 --kubernetes-version v1.13.2  --ignore-preflight-errors=NumCPU

export KUBECONFIG=/etc/kubernetes/admin.conf
```

### config kubectl env
```
echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> /etc/profile
source /etc/profile
echo $KUBECONFIG
```

### Control plane node isolation
```
kubectl taint nodes --all node-role.kubernetes.io/master-
```

## install calico network
```
kubectl apply -f https://docs.projectcalico.org/v3.4/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml

kubectl get pods --all-namespaces
```

## install dashboard
```
openssl req -newkey rsa:4096 -nodes -config ./certs/dashboard-openssl.cnf -days 365 -x509 -out ./certs/dashboard.crt -keyout ./certs/dashboard.key
# kubectl delete secrets kubernetes-dashboard-certs -n=kube-system
kubectl create secret generic kubernetes-dashboard-certs --from-file="./certs/dashboard.crt,./certs/dashboard.key" -n kube-system 
kubectl describe secret/$(kubectl get secret -nkube-system |grep admin |awk '{print $1}') -nkube-system

kubectl apply -f https://raw.githubusercontent.com/asktalk/install_kubernetes/master/plugins/dashboard/dashboard-user-role.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```

### Accessing dashboard
```
# https://xxx.xxx.xxx.xxx:32000
echo ">>>>>>>>>>>>>>>> kubernetes dashboard install finished. pls visit https://xxx.xxx.xxx.xxx:32000"
echo "kubernetes dashboard install finished. pwd is: "
kubectl describe secret/$(kubectl get secret -n=kube-system |grep admin |awk '{print $1}') -nkube-system
```

## Check out installation Success for kubernetes
```
kubectl get service --namespace=kube-system
kubectl get cs
kubectl get nodes
kubectl get pods --all-namespaces
```