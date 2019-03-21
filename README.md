# prom-helm
This repository contains helm charts for the PROM stack which includes:
```
prometheus-node-exporter
prometheus-server
alertmanager
grafana
```

The helm charts are from https://github.com/helm/charts/tree/master/stable


## To install the chart:
```
git clone https://github.com/satchpx/prom-helm.git
cd prom-helm/
helm install --name grafana --namespace monitoring grafana --set persistence.storageClassName=<portworx-storageClassName>
helm install --name prometheus --namespace monitoring prometheus --set server.persistentVolume.storageClass=<portworx-storageClassName>
```
The above is configured already to add prometheus as the datasource for grafana.

## @TODO:
Add a sidecar/ job to import dashboards to grafana
Currently helm chart does not import dashboards: https://github.com/helm/charts/issues/1079
NOTE: The dashboards are in `prom-helm/grafana/dashboards` directory
