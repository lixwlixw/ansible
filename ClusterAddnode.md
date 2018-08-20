## 描述Kubernetes集群添加node节点
1.配置hosts解析 
```
sudo echo 10.1.0.36 kubernetes.default.svc >> /etc/hosts
sudo echo 10.1.0.159 docker-repo.gaojihealth.cn >> /etc/hosts
```
2.docker 登陆私有镜像仓库(harbor)
```
sudo docker login -u xxx -p xxx docker-repo.gaojihealth.cn:80
```
3.更改主机名
```
sudo hostnamectl set-hostname nodexx.vm10.x.x.xcentos
```
4.在10.1.0.4上查看token
```
sudo kubeadm token list
```
4.节点加入集群
```
sudo kubeadm join --token=xxx.xxx kubernetes.default.svc:6443 
```
5.查看节点
```
sudo kubectl get node |grep nodexx
```
