# Get aggreation Kubernetes Utilization across any Pod Label
Useful links:
https://5pi.de/2017/11/09/use-prometheus-vector-matching-to-get-kubernetes-utilization-across-any-pod-label/

https://medium.com/@amimahloof/kubernetes-promql-prometheus-cpu-aggregation-walkthrough-2c6fd2f941eb

Query:

### CPU Aggregation label: sparkoperator.k8s.io/app-name=scala (label_sparkoperator_k8s_io_app_name)

```
sum by (label_sparkoperator_k8s_io_app_name) (
  rate(container_cpu_usage_seconds_total{cluster_id="CLUSTER_ID",namespace="demo-ns"}[2m]) * on (pod) group_left(label_sparkoperator_k8s_io_app_name)
  max by (pod,label_sparkoperator_k8s_io_app_name) (
  kube_pod_labels{label_sparkoperator_k8s_io_app_name!="",cluster_id="CLUSTER_ID",namespace="demo-ns"}
  )
)
```

### RAM Aggregation label: sparkoperator.k8s.io/app-name=scala (label_sparkoperator_k8s_io_app_name)

```
(sum by (label_sparkoperator_k8s_io_app_name) (
container_memory_working_set_bytes{namespace="demo-ns"} * on (pod) group_left(label_sparkoperator_k8s_io_app_name)
  max by (pod,label_sparkoperator_k8s_io_app_name) (
    kube_pod_labels{label_sparkoperator_k8s_io_app_name!="",namespace="demo-ns"}
  )
))/1024/1024/1024
