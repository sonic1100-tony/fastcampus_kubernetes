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

## Docker, containerd 설치 (모든 master, worker node)
### Docker 설치
RockyLinux 는 통상적인 CentOS 방식으로 설치가 되지 않고 아래 방식을 설치해야 한다.

```
sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo

sudo dnf update

# 충돌나는 패키지를 교체한다.(ex, podman)
sudo dnf install -y docker-ce docker-ce-cli containerd.io --allowerasing

docker --version

sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker

sudo usermod -aG docker 계정아이디
id 계정아이디

```
```
sudo mkdir -p /etc/docker
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker
```

