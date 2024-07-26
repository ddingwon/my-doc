# Install

여기에서는 Kubernetes install은 kube-spray를 이용해 설치하는 과정을 설명합니다.

## 1. git clone
```bash
git clone https://github.com/kubernetes-sigs/kubespray.git
```


## 2. Installing Ansible

- python venv package install

- ubuntu
```bash
apt install python3.10-venv -y
```


- venv setup file create
```bash
touch ansible_install.sh && vi ansible_install.sh

## 아래 내용 입력
#!/bin/bash
VENVDIR=kubespray-venv
KUBESPRAYDIR=kubespray

echo "Creating virtual environment..."
python3 -m venv $VENVDIR

echo "Activating virtual environment..."
source $VENVDIR/bin/activate

echo "Changing directory to Kubespray..."
cd $KUBESPRAYDIR

echo "Installing requirements..."
pip install -U -r requirements.txt

echo "Setup completed!"
```

- 실행 권한 부여
```bash
chmod +x ansible_install.sh
```

- 실행
```bash
./ansible_install.sh
```


## 3. kubespray 설정 (Deoply node)

Install Ansible according to [Ansible installation guide](https://github.com/kubernetes-sigs/kubespray/blob/master/docs/ansible.md#installing-ansible) then run the following steps:
- Copy ``inventory/sample`` as ``inventory/mycluster``

```bash
cp -rfp inventory/sample inventory/mycluster
```

- inventory.ini 파일 수정

```bash
[all]
m1 ansible_ssh_host=192.168.100.31 ip=192.168.100.31 etcd_member_name=etcd1
#m2 ansible_ssh_host=192.168.100.32 ip=192.168.100.32 etcd_member_name=etcd2
#m3 ansible_ssh_host=192.168.100.33 ip=192.168.100.33 etcd_member_name=etcd3
w1 ansible_ssh_host=192.168.100.31 ip=192.168.100.31
w2 ansible_ssh_host=192.168.100.32 ip=192.168.100.32
w3 ansible_ssh_host=192.168.100.33 ip=192.168.100.33

[kube_control_plane]
m1

[etcd]
m1

[kube_node]
w1
w2
w3

[k8s_cluster:children]
kube_node
kube_control_plane
calico_rr

[calico_rr]
```


- Ping Test

```bash title="inventory 파일내 특정 서버만 테스트"
# inventory 파일내 특정 서버만 테스트
ansible k8s-m1 -i /root/kubespray/inventory/mycluster/inventory.ini -m ping
```

```bash title="inventory 파일내 전체 서버만 테스트"
# inventory 파일내 전체 서버만 테스트
ansible all -i /root/kubespray/inventory/mycluster/inventory.ini -m ping
```


## 4. Deploy (배포)

- cluster 세부 설정 파일 위치 
- Review and change parameters under ``inventory/mycluster/group_vars``
```bash
cat inventory/mycluster/group_vars/all/all.yml
# NTP 설정 변경
ntp_enabled: true              # 기본 false 에서 true로 변경
ntp_manage_config: true        # 기본 false 에서 true로 변경
ntp_servers:
  - 192.168.100.11             # NTP 서버 정보 입

cat inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml
- container runtime, kube-proxy, kube version 등 설정 확인 및 변

cat inventory/mycluster/group_vars/k8s_cluster/addons.yml
dashboard_enabled: true            # true로 변경
metrics_server_enabled: true       # true로 변경 
ingress_nginx_enabled: true        # true로 변경

cat inventory/mycluster/group_vars/k8s_cluster/k8s-net-calico.yml
calico_network_backend: bird       # vxlan에서 bird로 변

```

- **Clean up old Kubernetes cluster with Ansible Playbook - run the playbook as root**
:::tip[TIP]
The option `--become` is required, as for example cleaning up SSL keys in /etc/, uninstalling old packages and interacting with various systemd daemons. Without --become the playbook will fail to run! And be mind it will remove the current kubernetes cluster (if it's running)!
:::

```bash
ansible-playbook -i inventory/mycluster/inventory.ini  --become --become-user=root reset.yml
```

 - **Deploy Kubespray with Ansible Playbook - run the playbook as root**
 
:::tip[TIP]
The option `--become` is required, as for example writing SSL keys in /etc/,  installing packages and interacting with various systemd daemons. Without --become the playbook will fail to run!
:::

```bash
cd /root/kubespray
```

```bash
ansible-playbook -i /root/k8s/kubespray/inventory/mycluster/inventory.ini  --become --become-user=root -e ansible_ssh_timeout=300 cluster.yml
```

deploy시 timeout 관련 에러 발생시 timeout 옵션 사용 
 ```bash
-e ansible_ssh_timeout=30
```

## 5. ETCD 환경변수 등록



설치된 마스트 서버 접속 후 `/root/.bashrc` 파일에 환경 변수 등록


```bash
export ETCDCTL_DIAL_TIMEOUT=3s
export ETCDCTL_CACERT=/etc/ssl/etcd/ssl/ca.pem
export ETCDCTL_CERT=/etc/ssl/etcd/ssl/node-mas1.pem         # mas1은 각 노드 hostname으로 변경
export ETCDCTL_KEY=/etc/ssl/etcd/ssl/node-mas1-key.pem      # mas1은 각 노드 hostname으로 변경
```

ETCD 상태 확인

```bash
/usr/local/bin/etcdctl endpoint --cluster status -w table

+----------------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
|          ENDPOINT          |        ID        | VERSION | DB SIZE | IS LEADER | IS LEARNER | RAFT TERM | RAFT INDEX | RAFT APPLIED INDEX | ERRORS |
+----------------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
| https://192.168.47.33:2379 | 44ee6cf5740f6cc4 |  3.5.10 |   11 MB |     false |      false |         5 |       6862 |               6862 |        |
| https://192.168.47.32:2379 | 60aef93527714fb3 |  3.5.10 |   11 MB |     false |      false |         5 |       6862 |               6862 |        |
| https://192.168.47.31:2379 | afe363d17a4e8dfc |  3.5.10 |   11 MB |      true |      false |         5 |       6862 |               6862 |        |
+----------------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
```

```bash
/usr/local/bin/etcdctl endpoint --cluster health -w table
+-----------------------------+--------+--------------+---------------------------+
|          ENDPOINT           | HEALTH |     TOOK     |           ERROR           |
+-----------------------------+--------+--------------+---------------------------+
| https://192.168.100.22:2379 |   true |  35.016885ms |                           |
| https://192.168.100.23:2379 |   true |   38.29122ms |                           |
| https://192.168.100.21:2379 |  false | 5.001113954s | context deadline exceeded |
+-----------------------------+--------+--------------+---------------------------+

```

```bash
/usr/local/bin/etcdctl member list -w table
+------------------+---------+-------+----------------------------+----------------------------+------------+
|        ID        | STATUS  | NAME  |         PEER ADDRS         |        CLIENT ADDRS        | IS LEARNER |
+------------------+---------+-------+----------------------------+----------------------------+------------+
| 44ee6cf5740f6cc4 | started | etcd3 | https://192.168.47.33:2380 | https://192.168.47.33:2379 |      false |
| 60aef93527714fb3 | started | etcd2 | https://192.168.47.32:2380 | https://192.168.47.32:2379 |      false |
| afe363d17a4e8dfc | started | etcd1 | https://192.168.47.31:2380 | https://192.168.47.31:2379 |      false |
+------------------+---------+-------+----------------------------+----------------------------+------------+
```
-----
## 6. error

```bash
Wait for etcd cluster to be healthy 

Tuesday 19 March 2024  22:26:04 +0700 (0:00:00.142)       0:16:24.843 *********
FAILED - RETRYING: [k8s-m1]: Configure | Wait for etcd cluster to be healthy (4 retries left).
FAILED - RETRYING: [k8s-m1]: Configure | Wait for etcd cluster to be healthy (3 retries left).
FAILED - RETRYING: [k8s-m1]: Configure | Wait for etcd cluster to be healthy (2 retries left).
FAILED - RETRYING: [k8s-m1]: Configure | Wait for etcd cluster to be healthy (1 retries left).

TASK [etcd : Configure | Wait for etcd cluster to be healthy] ************************************************************************************************************************************
fatal: [k8s-m1]: FAILED! => {"attempts": 4, "changed": false, "cmd": "set -o pipefail && /usr/local/bin/etcdctl endpoint --cluster status && /usr/local/bin/etcdctl endpoint --cluster health 2>&1 | grep -v 'Error: unhealthy cluster' >/dev/null", "delta": "0:00:05.143499", "end": "2024-03-19 22:26:47.415809", "msg": "non-zero return code", "rc": 1, "start": "2024-03-19 22:26:42.272310", "stderr": "{\"level\":\"warn\",\"ts\":\"2024-03-19T22:26:47.398252+0700\",\"logger\":\"etcd-client\",\"caller\":\"v3@v3.5.10/retry_interceptor.go:62\",\"msg\":\"retrying of unary invoker failed\",\"target\":\"etcd-endpoints://0xc000026000/192.168.100.21:2379\",\"attempt\":0,\"error\":\"rpc error: code = DeadlineExceeded desc = context deadline exceeded\"}\nFailed to get the status of endpoint https://192.168.100.23:2379 (context deadline exceeded)", "stderr_lines": ["{\"level\":\"warn\",\"ts\":\"2024-03-19T22:26:47.398252+0700\",\"logger\":\"etcd-client\",\"caller\":\"v3@v3.5.10/retry_interceptor.go:62\",\"msg\":\"retrying of unary invoker failed\",\"target\":\"etcd-endpoints://0xc000026000/192.168.100.21:2379\",\"attempt\":0,\"error\":\"rpc error: code = DeadlineExceeded desc = context deadline exceeded\"}", "Failed to get the status of endpoint https://192.168.100.23:2379 (context deadline exceeded)"], "stdout": "https://192.168.100.21:2379, 1a9c8b7657984a8d, 3.5.10, 12 MB, true, false, 6, 6009, 6009, \nhttps://192.168.100.22:2379, 64263c5128ac1157, 3.5.10, 12 MB, false, false, 6, 6021, 6021, ", "stdout_lines": ["https://192.168.100.21:2379, 1a9c8b7657984a8d, 3.5.10, 12 MB, true, false, 6, 6009, 6009, ", "https://192.168.100.22:2379, 64263c5128ac1157, 3.5.10, 12 MB, false, false, 6, 6021, 6021, "]}
```

- case : 노드 1 비정상 종료 후 아래 처럼 상태 체크 에러 발생. 
- 22번으로 마스터로 변경 되어 있었고 1번에서는 etcd 서비스 데몬 기동이 안되는 상황. 재시작 해도 동일증상. 2번 서버 리부팅을 하니 1번 서버 멤버로 상태 정상 돌아왔고 마스터는 3번으로 변경 되었음. 

```bash
/usr/local/bin/etcdctl endpoint --cluster health -w table 
+-----------------------------+--------+--------------+---------------------------+
|          ENDPOINT           | HEALTH |     TOOK     |           ERROR           |
+-----------------------------+--------+--------------+---------------------------+
| https://192.168.100.22:2379 |   true |  35.016885ms |                           |
| https://192.168.100.23:2379 |   true |   38.29122ms |                           |
| https://192.168.100.21:2379 |  false | 5.001113954s | context deadline exceeded |
+-----------------------------+--------+--------------+---------------------------+
```
## 7. kubectl 설치

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
mv kubectl /usr/local/bin
```


## 8. Metric-sever 설치

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

```bash
kubectl edit dedit describe metrics-server -n kube-system
```

```bash
    Args:
      --cert-dir=/tmp
      --secure-port=10250
      --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
      --kubelet-use-node-status-port
      --kubelet-insecure-tls=true               # 이부분 추가
      --metric-resolution=15s
```