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
* 

