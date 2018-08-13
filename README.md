# Ansible
Ansible是一个部署一群远程主机的工具。远程的主机可以是远程虚拟机或物理机， 也可以是本地主机。
Ansible通过SSH协议实现远程节点和管理节点之间的通信。理论上说，只要管理员通过ssh登录到一台远程主机上能做的操作，Ansible都可以做到。
## 1. 安装
```
yum install ansible || apt-get install ansible
```
## 2. 基础用法
### 2.1 准备ansible hosts文件
cat hosts
```
10.1.0.2 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
10.1.0.3 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
10.1.0.5 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
10.1.0.6 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
10.1.0.7 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
10.1.0.9 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
```
