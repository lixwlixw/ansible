## 1.启动node-exporter、kube-state-metrics。   

```
kubectl create -f https://github.com/lixwlixw/monitroing/blob/master/node-exporter.yaml -n monitoring
kubectl create -f https://github.com/lixwlixw/monitroing/blob/master/kube-state-metrics.yaml -n monitoring
```
