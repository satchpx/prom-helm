# prom-helm
This repository contains helm charts for the PROM stack which includes:
```
prometheus-node-exporter
prometheus-server
alertmanager
grafana
```

The helm charts are from https://github.com/helm/charts/tree/master/stable


## To install portworx:
```
https://docs.portworx.com/portworx-install-with-kubernetes/
```

##  After installing portworx, create a storageClass to be used
```
kind: StorageClass
apiVersion: storage.k8s.io/v1beta1
metadata:
  name: px-high-rf2
provisioner: kubernetes.io/portworx-volume
parameters:
  fs: "ext4"
  block_size: "4k"
  shared: "false"
  repl: "2"
  snap_interval:   "0"
  priority_io:  "high"
```

## To install the chart:
```
git clone https://github.com/satchpx/prom-helm.git
cd prom-helm/
helm install --name prometheus --namespace monitoring prometheus --values=prometheus/values-prod.yaml --set server.persistentVolume.storageClass=<portworx-storageClassName>
helm install --name grafana --namespace monitoring grafana --values=grafana/values-prod.yaml --set persistence.storageClassName=<portworx-storageClassName>
```
The above is configured already to add prometheus as the datasource for grafana.

## To login to grafana:
1. NOTE: The chart generates a random password for the admin user. To specify your own, either update `values.yaml` or set is in the `helm install` command using `--set adminPassword=<myStrongPassword>`. Alternatively, you can also reference a kubernetes secret containing the password key using `--set admin.passwordKey=<secretName>`

  Otherwise, to access grafana UI without any of the above, get the random password using:
```
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```
2. The Grafana server can be accessed via port 80 on the following DNS name from within your cluster: `grafana.monitoring.svc.cluster.local`

   Get the Grafana URL to visit by running these commands in the same shell:
```
  export POD_NAME=$(kubectl get pods --namespace monitoring -l "app=grafana,release=grafana" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace monitoring port-forward $POD_NAME 3000
```
3. Login with the password from step 1 and the username: admin
