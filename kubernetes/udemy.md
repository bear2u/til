gcloud : naverbear13



kubectl switch minikube or gcloud



kubectl config use-context CONTEXT_NAME

to list all cotexts:

kubectl config get-contexts



tiller



```
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule - -clusterrole=cluster-admin - -serviceaccount=kube-system:tiller
```



```
helm install stable/nginx-ingress --name my-nginx --set rbac.create=true
```

