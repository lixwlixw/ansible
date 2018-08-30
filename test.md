## 1.启动node-exporter、kube-state-metrics。   

```
kubectl create -f https://github.com/lixwlixw/monitroing/blob/master/node-exporter.yaml -n monitoring
kubectl create -f https://github.com/lixwlixw/monitroing/blob/master/kube-state-metrics.yaml -n monitoring
```

## 2.修改prometheus配置文件(/usr/share/prometheus/prometheus.yml)。
```
global:
  scrape_interval: 15s         #每15秒pull一次数据。
  evaluation_interval: 30s     #每30秒检测一次报警规则。
```
```
  - job_name: 'prometheus-node-exporter'   #抽取每个节点上node-exporter的数据。

    tls_config:
      ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
    kubernetes_sd_configs:
    - role: endpoints
    relabel_configs:
    - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape, __meta_kubernetes_endpoint_port_name]
      regex: true;node-exporter
      action: keep
    - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scheme]
      action: replace
      target_label: __scheme__
      regex: (https?)
    - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
      action: replace
      target_label: __metrics_path__
      regex: (.+)
    - source_labels: [__address__, __meta_kubernetes_service_annotation_prometheus_io_port]
      action: replace
      target_label: __address__
      regex: (.+)(?::\d+);(\d+)
      replacement: $1:$2
    - action: labelmap
      regex: __meta_kubernetes_service_label_(.+)
    - source_labels: [__meta_kubernetes_namespace]
      action: replace
      target_label: kubernetes_namespace
    - source_labels: [__meta_kubernetes_service_name]
      action: replace
      target_label: kubernetes_name
```
```
  - job_name: 'kube-state-metrics'     #抽取kube-state-metrics的数据。

    tls_config:
      ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
    kubernetes_sd_configs:
    - role: node
    relabel_configs:
    - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape,__meta_kubernetes_endpoint_port_name]
      regex: true;kube-state-metrics
      action: keep
    - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scheme]
      action: replace
      target_label: __scheme__
      regex: (https?)
    - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
      action: replace
      target_label: __metrics_path__
      regex: (.+)
    - source_labels: [__address__, __meta_kubernetes_service_annotation_prometheus_io_port]
      action: replace
      target_label: __address__
      regex: (.+)(?::\d+);(\d+)
      replacement: 10.108.239.40:8080
    - action: labelmap
      regex: __meta_kubernetes_service_label_(.+)
    - source_labels: [__meta_kubernetes_namespace]
      action: replace
      target_label: kubernetes_namespace
    - source_labels: [__meta_kubernetes_service_name]
      action: replace
      target_label: kubernetes_name
```

## 3.更改prometheus启动参数(deployment yaml)。
```
        - args:
          - --config.file=/usr/share/prometheus/prometheus.yml
          - --storage.tsdb.path=/usr/share/prometheus/storage
          - --storage.tsdb.retention=3d                          #历史数据保留3天。
          - --web.enable-lifecycle
```

## 4.添加alert报警规则(/usr/share/prometheus/rules/base.yml)。
```
groups:
- name: base-rule
  rules:
  - alert: cluster-production-01-vm-down-warning     #node节点状态监控，状态为0(down)超过1分钟时报警。
    expr: up{app="node-exporter"} == 0
    for: 1m
    labels:
      team: node
    annotations:
      summary: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 1 minutes."
      description: "node: {{$labels.instance}}  job: {{$labels.job}}  hang up (current value is: {{ $value }}"

  - alert: cluster-production-01-etcd-down-warning  #报警条件：etcd节点状态监控，状态为0(down)持续1分钟。
    expr: up{job="etcd"} == 0
    for: 1m
    labels:
      team: node
    annotations:
      description: '{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 1 minutes.'
      summary: 'Instance {{ $labels.instance }} down'

  - alert: cluster-production-01-vm-memory-warning  #报警条件：node节点使用内存超过80%，持续2分钟。
    expr: (node_memory_MemTotal_bytes - (node_memory_MemFree_bytes+node_memory_Buffers_bytes+node_memory_Cached_bytes )) / node_memory_MemTotal_bytes * 100 > 80 
    for: 2m
    labels:
      team: node
    annotations:
      summary: "{{$labels.instance}}: High Memory usage detected"
      description: "{{$labels.instance}}: Memory usage is above 80% (current value is: {{ $value }}"
  - alert: cluster-production-01-vm-cpu-warning     #报警条件：node节点每核CPUidle小于10%，持续两分钟。
    expr: rate(node_cpu_seconds_total{mode="idle"}[1m]) * 100 < 10 
    for: 2m
    labels:
      team: node
    annotations:
      summary: "{{$labels.instance}}: High CPU usage detected"
      description: "{{$labels.instance}}: CPU usage is above 90% (current value is: {{ $value }}"

  - alert: cluster-production-01-vm-disk-warning    #报警条件：node节点磁盘使用小于30G，持续两分钟。                               
    expr: node_filesystem_avail_bytes{device="/dev/vda1",mountpoint="/etc/hostname"} / 1024 / 1024 / 1024 < 30                   
    for: 2m                                                                                         
    labels:                                                                                         
      team: node                                                  
    annotations:                                                  
      summary: "{{$labels.instance}}: High Disk usage detected"
      description: "{{$labels.instance}}: Disk free < 30GB  (current value is: {{ $value }}"

  - alert: cluster-production-01-deployment-warning  #报警条件：deployment预期副本数小于实际pod数，持续一分钟。                                                                
    expr: kube_deployment_status_replicas_available  < kube_deployment_spec_replicas
    for: 1m                       
    labels:                                                                                                   
      team: node                                                                                              
    annotations:                                                                                              
      summary: "{{$labels.namespace}}:{{$labels.deployment}} replicas Error"                                               
      description: "{{$labels.deployment}} replicas status is: {{ $value }}"
```
## 5....
