# install_baremetal.md

해당 문서는 Openfx의 배포 테스트 환경 설치에 대한 가이드라인 문서입니다. 

![Openfx_baremetal](https://github.com/ksw19627/OpenFx_guide/blob/master/Serverless_1.png)



## I. docker 설치 

[Docker 설치](https://docs.docker.com/install/linux/docker-ce/centos/#install-using-the-repository)



## II. Kubespray를 통한 Kubernetes Cluster와 Docker 구축 

'Kubespray'는 인프라 구성도구인 'Ansible'을 사용하기 때문에 Master로 사용될 하나의 노드에서만 실행하면 됩니다. Master에서 다음과 같은 과정을 수행합니다. 



> NOTE : 최신버전인 'Kubespray v2.7.0' 은 버그가 있어서 사용하지 않고 있습니다. 그러나 'Nvidia-docker' 설치를 지원하기 때문에 추후에 버그가 패치되면 업그레이드를 고려해야 합니다. 



### 1. 클러스터를 구성할 노드들에 ssh key를 복사

```bash
$ ssh-keygen -t rsa
$ ssh-copy-id -i ~/.ssh/id_rsa.pub root@"YOUR_MASTER_IP"
$ ssh-copy-id -i ~/.ssh/id_rsa.pub root@"YOUR_NODE_IP"
```



### 2. Kubespray v2.6.0 다운로드 및 폴더 진입 


```bash
$ wget https://github.com/kubernetes-incubator/kubespray/archive/v2.6.0.tar.gz
$ tar xvf v2.6.0.tar.gz
$ cd kubespray-2.6.0

>>
kubespray-2.6.0
├── ansible.cfg
├── code-of-conduct.md
├── Dockerfile
├── inventory
├── OWNERS
├── remove-node.yml
├── roles
├── SECURITY_CONTACTS
├── tests
├── cluster.retry
├── contrib
├── ... etc


```



### 3. Kubespray v2.6.0에 필요한 라이브러리 설치 

```bash
$ yum --enablerepo=extras install epel-release
$ yum install python3 python-pip
$ sudo pip install -r requirements.txt
```



### 4. Ansible inventory 파일 업데이트


```bash
$ cp -rfp inventory/sample inventory/mycluster
$ declare -a IPS=("YOUR_MASTER_IP" "YOUR_NODE_IP")
$ CONFIG_FILE=inventory/mycluster/hosts.ini python3 contrib/inventory_builder/inventory.py ${IPS[@]}
```



### 5. etcd 노드가 홀수가 되도록, 클러스터 구성에 맞게 변경

> NOTE : 다운받은 Kubespray v2.6.0 폴더 경로에서 작업 할 것  

```bash
$ vi inventory/mycluster/hosts.ini

kubespray-2.6.0
├─ inventory
   ├─ local
   ├─ sample
   ├─ mycluster
      ├─ group_vars
      ├─ sample
      ├─ hosts.ini
├─ ... etc      
```



hosts.ini 파일을 다음과 같이 수정

```bash
[all]
node1    ansible_host="YOUR_MASTER_IP" ip="YOUR_MASTER_IP"
node2    ansible_host="YOUR_NODE_IP" ip="YOUR_NODE_IP"

[kube-master]
node1

[kube-node]
node2

[etcd]
node1

[k8s-cluster:children]
kube-node
kube-master

[calico-rr]

[vault]
node1
node2
```



### 6. 필요 docker-option 이나 기타 option 등 설정

> NOTE : 다운받은 Kubespray v2.6.0 폴더 경로에서 작업 할 것  

```bash
$ vi inventory/mycluster/group_vars/k8s-cluster.yml

kubespray-2.6.0
├─ inventory
   ├─ local
   ├─ sample
   ├─ mycluster
      ├─ group_vars
          ├─ all.yml
          ├─ k8s_cluster.yml 
├─ ... etc   
```

k8s-cluster.yml 파일에서

```bash
docker_options: "--insecure-registry={{ kube_service_addresses }} 
--graph={{ docker_daemon_graph }}  {{ docker_log_opts }}"
```

해당 부분을 다음과 같이 수정

```bash
docker_options: "--insecure-registry="YOUR_MASTER_IP":5000 --insecure-registry={{ kube_service_addresses }} 
--graph={{ docker_daemon_graph }}  {{ docker_log_opts }}"
```


앞서 모든 설정이 완료되면, 다음과 같은 명령을 통해 Kubernetes cluster를 구성합니다. 



### 7. Install Kubernetes Cluster

```bash
$ ansible-playbook -i inventory/mycluster/hosts.ini --become --become-user=root cluster.yml
```



## III. Trouble Shooting 

'Kubespray'를 통한 Kubernetes Cluster 설치 중에 가장 빈번히 일어나는 문제와 그 문제 해결을 정리하였습니다.



| 문제           | 원인확인                            | 원인                                            | 해결                                  |
| -------------- | ----------------------------- | ------------------------------------------ | ------------------------------- |
|                | 지능 컴포넌트의 이름                                         | echo-service                                                 |      |
| runtime        | 지능 컴포넌트가 실행될 환경                                  | python3                                                      |      |
| image          | 지능 컴포넌트 이미지 이름과 버전<br>(레포지토리인 keti.ausscomm.com:5001은 고정) | keti.asuscomm.com:5001/echo-service:v1                       |      |
| handler        | 지능 컴포넌트 배포시에 실행되는 엔트리포인트 정보            | handler:<br>&nbsp; name: Handler<br>&nbsp; dir: ./echo-service<br>&nbsp; file: "handler.py" |      |
| maintainer     | (optional)지능 컴포넌트 개발자 또는 유지보수 가능한 사람의 정보 | KETI                                                         |      |
| desc           | (optional)지능 컴포넌트 용도/설명                            | This is ....                                                 |      |
| environment    | (optional)런타임 내에서 사용할 환경 변수                     | environment:<br>&nbsp; - "PATH=/usr/local/bin"               |      |
| skip_build     | (optional)지능 컴포넌트 빌드 및 레포지토리에 저장 단계 건너뛰기 | skip_build: true                                             |      |
| limits         | (optional)지능 컴포넌트가 사용할 자원 요청 및 제한           | limits:<br>&nbsp; cpu: "1"<br>&nbsp; gpu: "1"<br>&nbsp; memory: "1G" |      |
| build_args     | Dockerfile내에 ARG 값 지정                                   | build_args:<br>&nbsp; - "PYTHON_VERSION=3.7"                 |      |
| build_packages | Dockerfile내의 apt 패키지 관리자인 ADDITIONAL_PACKAGE 값으로 설정 (이 필드를 통해 원하는 패키지를 설치할 수 있습니다.) | build_packages:<br>&nbsp; -make<br>&nbsp; -python3-pip<br>&nbsp; -gcc<br>&nbsp; -python-numpy |      |
