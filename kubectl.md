# kubectl常用命令介绍
## get
* 列出demo namespaces下所有pod信息
```
kubectl get pod -n demo
```
* 列出demo namespaces下名为test的pod的yaml
```
kubectl get pod test -o yaml -n demo
```
* 列出demo namespaces下所有pod以及pod所在节点信息
```
kubectl get pod -o wide -n demo
```
* 列出demo namespaces下所有pod信息并持续显示
```
kubectl get pod -w -n demo
```
* 列出demo namespaces下所有pod，deployment，service，ingress信息
```
kubectl get pod,deployment,svc,ingress -n demo
```
* 列出demo namespaces下所有资源信息
```
kubectl get all -n demo
```
## logs
* 打印demo namespaces下名为test pod的日志
```
kubectl logs test -n demo
```
* 打印demo namespaces下名为test pod的日志并且持续打印
```
kubectl logs -f test -n demo
```
* 打印demo namespaces下名为test pod中test1容器的日志
```
kubectl logs -f test -c test1 -n demo
```
* 打印demo namespaces下名为test pod的日志后100行
```
kubectl logs --tail=100 test -n demo
```
## exec
* 以sh命令行前台进入demo namespaces下名为test pod
```
kubectl exec -it test sh -n demo
```
* 以sh命令行前台进入demo namespaces下名为test pod中test1容器
```
kubectl exec -it test -c test1 -n demo
```
* 进入demo namespaces下名为test pod执行命令
```
kubectl exec test ls / -n demo
```
## 集群操作
* 停止node1节点调度
```
kubectl cordon node1
```
* 驱逐node1节点上所有pod(先执行停止调度操作)
```
kubectl drain node1 --force
```
* 把node1节点从集群中删除(先执行上面两步操作)
```
kubectl delete node node1
```
* 恢复node1节点调度
```
kubectl uncordon node1
```
