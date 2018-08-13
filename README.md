# Ansible
Ansible是一种IT自动化工具。它可以配置系统，部署软件以及协调更高级的IT任务，例如持续部署或零停机滚动更新。
Ansible的主要目标是简单易用。它还非常注重安全性和可靠性，具有最少的移动部件，使用OpenSSH进行传输（使用其他传输和拉动模式作为替代方案），以及围绕人类可审计性设计的语言 - 即使是那些不熟悉的人该程序。
## 1. 安装
```
yum install ansible || apt-get install ansible
```
## 2. 基础用法
```
ansible -i hosts(-i指定hosts文件路径)  all(all表示hosts文件所有主机 可以分组执行) -m shell(-m 指定模块)  -a "echo demo"(具体执行的命令)
```

### 2.1. 准备ansible hosts文件  
```
[devuser@node02 demo]$ cat hosts

10.1.0.2 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
10.1.0.3 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
10.1.0.5 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
10.1.0.6 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
10.1.0.7 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
10.1.0.9 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
```
第一列为需要远程主机IP。    
第二列为远程使用的用户。        
第三列为远程用户的密码。       
ansible是通过ssh远程主机。认证方式 1.密码认证 2.密钥认证 本次演示使用密码认证。  

### 2.2. 查看hosts文件中所有机器内存使用情况。
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
### 2.3. 将本地1.txt文件拷贝到远程主机devuser用户的家目录。
```
[devuser@node02 demo]$ ansible -i hosts all -m copy -a "src=1.txt dest=/home/devuser/"

10.1.0.5 | SUCCESS => {
    "changed": true, 
    "checksum": "4e1243bd22c66e76c2ba9eddc1f91394e57f9f83", 
    "dest": "/home/devuser/1.txt", 
    "gid": 2047, 
    "group": "devuser", 
    "md5sum": "d8e8fca2dc0f896fd7cb4cb0031ba249", 
    "mode": "0664", 
    "owner": "devuser", 
    "size": 5, 
    "src": "/home/devuser/.ansible/tmp/ansible-tmp-1534154182.27-122234934217791/source", 
    "state": "file", 
    "uid": 2047
}
10.1.0.3 | SUCCESS => {
    "changed": true, 
    "checksum": "4e1243bd22c66e76c2ba9eddc1f91394e57f9f83", 
    "dest": "/home/devuser/1.txt", 
    "gid": 2047, 
    "group": "devuser", 
    "md5sum": "d8e8fca2dc0f896fd7cb4cb0031ba249", 
    "mode": "0664", 
    "owner": "devuser", 
    "size": 5, 
    "src": "/home/devuser/.ansible/tmp/ansible-tmp-1534154182.26-186234237051351/source", 
    "state": "file", 
    "uid": 2047
}
10.1.0.7 | SUCCESS => {
    "changed": true, 
    "checksum": "4e1243bd22c66e76c2ba9eddc1f91394e57f9f83", 
    "dest": "/home/devuser/1.txt", 
    "gid": 2047, 
    "group": "devuser", 
    "md5sum": "d8e8fca2dc0f896fd7cb4cb0031ba249", 
    "mode": "0664", 
    "owner": "devuser", 
    "size": 5, 
    "src": "/home/devuser/.ansible/tmp/ansible-tmp-1534154182.29-15280594574344/source", 
    "state": "file", 
    "uid": 2047
}
10.1.0.2 | SUCCESS => {
    "changed": true, 
    "checksum": "4e1243bd22c66e76c2ba9eddc1f91394e57f9f83", 
    "dest": "/home/devuser/1.txt", 
    "gid": 2047, 
    "group": "devuser", 
    "md5sum": "d8e8fca2dc0f896fd7cb4cb0031ba249", 
    "mode": "0664", 
    "owner": "devuser", 
    "size": 5, 
    "src": "/home/devuser/.ansible/tmp/ansible-tmp-1534154182.24-256472349789830/source", 
    "state": "file", 
    "uid": 2047
}
10.1.0.6 | SUCCESS => {
    "changed": true, 
    "checksum": "4e1243bd22c66e76c2ba9eddc1f91394e57f9f83", 
    "dest": "/home/devuser/1.txt", 
    "gid": 2047, 
    "group": "devuser", 
    "md5sum": "d8e8fca2dc0f896fd7cb4cb0031ba249", 
    "mode": "0664", 
    "owner": "devuser", 
    "size": 5, 
    "src": "/home/devuser/.ansible/tmp/ansible-tmp-1534154182.29-138161275507008/source", 
    "state": "file", 
    "uid": 2047
}
10.1.0.9 | SUCCESS => {
    "changed": true, 
    "checksum": "4e1243bd22c66e76c2ba9eddc1f91394e57f9f83", 
    "dest": "/home/devuser/1.txt", 
    "gid": 2047, 
    "group": "devuser", 
    "md5sum": "d8e8fca2dc0f896fd7cb4cb0031ba249", 
    "mode": "0664", 
    "owner": "devuser", 
    "size": 5, 
    "src": "/home/devuser/.ansible/tmp/ansible-tmp-1534154183.42-19923589709942/source", 
    "state": "file", 
    "uid": 2047
}
```
### 2.4. 查看2.3中拷贝的1.txt文件。
```
[devuser@node02 demo]$ ansible -i hosts  all -m shell -a "ls /home/devuser/ && cat /home/devuser/1.txt"

10.1.0.7 | SUCCESS | rc=0 >>
1.txt
test

10.1.0.5 | SUCCESS | rc=0 >>
1.txt
test

10.1.0.6 | SUCCESS | rc=0 >>
1.txt
test

10.1.0.3 | SUCCESS | rc=0 >>
1.txt
test

10.1.0.2 | SUCCESS | rc=0 >>
1.txt
test

10.1.0.9 | SUCCESS | rc=0 >>
1.txt
test

```
### 2.5. 删除2.3中拷贝的1.txt文件。
```
[devuser@node02 demo]$ ansible -i hosts  all -m shell -a "rm -f  /home/devuser/1.txt"


10.1.0.5 | SUCCESS | rc=0 >>


10.1.0.7 | SUCCESS | rc=0 >>


10.1.0.3 | SUCCESS | rc=0 >>


10.1.0.6 | SUCCESS | rc=0 >>


10.1.0.2 | SUCCESS | rc=0 >>


10.1.0.9 | SUCCESS | rc=0 >>
```
### 2.6. 验证删除。
```
[devuser@node02 demo]$ ansible -i hosts  all -m shell -a "cat /home/devuser/1.txt"

10.1.0.5 | FAILED | rc=1 >>
cat: /home/devuser/1.txt: 没有那个文件或目录non-zero return code

10.1.0.3 | FAILED | rc=1 >>
cat: /home/devuser/1.txt: 没有那个文件或目录non-zero return code

10.1.0.7 | FAILED | rc=1 >>
cat: /home/devuser/1.txt: 没有那个文件或目录non-zero return code

10.1.0.6 | FAILED | rc=1 >>
cat: /home/devuser/1.txt: 没有那个文件或目录non-zero return code

10.1.0.2 | FAILED | rc=1 >>
cat: /home/devuser/1.txt: 没有那个文件或目录non-zero return code

10.1.0.9 | FAILED | rc=1 >>
cat: /home/devuser/1.txt: 没有那个文件或目录non-zero return code

```
### 2.7. 分组执行
```
[devuser@node02 demo]$ cat hosts 

[group1]
10.1.0.2 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
10.1.0.3 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
10.1.0.5 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
[group2]
10.1.0.6 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
10.1.0.7 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
10.1.0.9 ansible_ssh_user=devuser ansible_ssh_pass=xxxxx
```
### 2.8. 查看group1中主机内存使用情况。
```
[devuser@node02 demo]$ ansible -i hosts group1 -m shell -a "free -h"

10.1.0.3 | SUCCESS | rc=0 >>
              total        used        free      shared  buff/cache   available
Mem:           7.6G        4.4G        166M        2.7M        3.0G        2.8G
Swap:            0B          0B          0B

10.1.0.5 | SUCCESS | rc=0 >>
              total        used        free      shared  buff/cache   available
Mem:           7.6G        2.5G        2.9G        2.4M        2.3G        4.8G
Swap:            0B          0B          0B

10.1.0.2 | SUCCESS | rc=0 >>
              total        used        free      shared  buff/cache   available
Mem:           7.6G        2.6G        3.1G        2.6M        1.9G        4.7G
Swap:            0B          0B          0B

```
## 3. 高级用法
请参考 https://docs.ansible.com/ansible/latest/user_guide/playbooks.html
