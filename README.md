# Ansible
Ansible是一个部署一群远程主机的工具。远程的主机可以是远程虚拟机或物理机， 也可以是本地主机。
Ansible通过SSH协议实现远程节点和管理节点之间的通信。理论上说，只要管理员通过ssh登录到一台远程主机上能做的操作，Ansible都可以做到。
## 1. 安装
```
yum install ansible || apt-get install ansible
```
## 2. 基础用法
### 2.1 准备ansible hosts文件  
```
cat hosts
10.1.0.2 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
10.1.0.3 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
10.1.0.5 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
10.1.0.6 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
10.1.0.7 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
10.1.0.9 ansible_ssh_user=devuser ansible_ssh_pass=9aoji_D@v
```
第一列为需要远程主机IP。    
第二列为远程使用的用户。        
第三列为远程用户的密码。       
ansible是通过ssh远程主机。认证方式 1.密码认证 2.密钥认证 本次演示使用密码认证。  

### 2.2 查看hosts文件中所有机器内存使用情况。
```
[devuser@node02 demo]$ ansible -i hosts all -m shell -a "free -h"
10.1.0.7 | SUCCESS | rc=0 >>
              total        used        free      shared  buff/cache   available
Mem:           7.6G        2.7G        1.6G        2.5M        3.4G        4.6G
Swap:            0B          0B          0B

10.1.0.5 | SUCCESS | rc=0 >>
              total        used        free      shared  buff/cache   available
Mem:           7.6G        2.5G        3.0G        2.4M        2.2G        4.8G
Swap:            0B          0B          0B

10.1.0.3 | SUCCESS | rc=0 >>
              total        used        free      shared  buff/cache   available
Mem:           7.6G        4.4G        160M        2.7M        3.1G        2.8G
Swap:            0B          0B          0B

10.1.0.2 | SUCCESS | rc=0 >>
              total        used        free      shared  buff/cache   available
Mem:           7.6G        2.4G        3.4G        2.6M        1.8G        4.8G
Swap:            0B          0B          0B

10.1.0.6 | SUCCESS | rc=0 >>
              total        used        free      shared  buff/cache   available
Mem:           7.6G        1.6G        3.6G        3.3M        2.5G        5.6G
Swap:            0B          0B          0B

10.1.0.9 | SUCCESS | rc=0 >>
              total        used        free      shared  buff/cache   available
Mem:           7.6G        3.4G        141M        2.2M        4.1G        3.9G
Swap:            0B          0B          0B

```
