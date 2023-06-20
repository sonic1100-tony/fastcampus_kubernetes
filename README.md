# How to Install Kubernetes Cluster on Rocky Linux
## 사전 준비
### sudo 계정생성
```
useradd -d /home/rocky rocky
echo "password" | passwd 1111 --stdin

chmod 700 /usr/bin/su

sed -ie '/wheel/s/$/\:rocky/' /etc/group

vi /etc/sudoers
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
rocky   ALL=(ALL)       ALL

```

### 방화벽 off
```
sudo systemctl stop firewalld && sudo systemctl disable firewalld

sudo setenforce 0
sudo sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### Swap off
```
sudo swapoff -a
sudo sed -i -e '/swap/d' /etc/fstab
```

### hosts 파일 수정(옵션)
```
vi /etc/hosts
---------------------------
192.168.0.89    node-01
192.168.0.90    node-02

---------------------------
```


