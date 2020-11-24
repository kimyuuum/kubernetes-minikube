# Kubernetes 

### Master Node

**Minikube** : Kubernetes를 로컬에서 쉽게 실행하는 도구. VM이나 노트북에서 단일 노드 k8s cluster를 실행한다.

→ 로컬에서 실습을 진행할 경우 단일 노드도 충분하다는 의견이 존재함

**Kubeadm :** Multi node cluster를 on-premise에서 실행한다.

### Object

basic object가 가장 기본적인 구성 단위를 이룬다. 이것의 생성 주기를 관리하는 controller가 또 존재한다. 

Object의 spec

커맨드라인을 통해서 object 생성시, spec에 기술한 file(yaml / json)을 인자로 전달 가능하다.

Basic Object

컨테이너화되어 배포되는 애플리케이션을 기술한다. pod, service, volume, namespace등이 있다.

1. **Pod**

    가장 기본이 되는 배포 단위. 컨테이너를 포함한다.

    ex) Pod를 정의한 object spec

    ```yaml
    apiVersion : v1 # 스크립트 실행을 위한 API 버전
    kind : Pod # 리소스 종류
    metadata :
      name : nginx
    spec : 
      containers : # Pod는 컨테이너를 포함하므로 기술한다.
        - name : nginx
        image : nginx:1.7.9  #도커 이미지
      ports : 
        - containerPort : 8090 # 컨테이너 포트
    ```

    **Pod의 특징**

    - Pod 내의 컨테이너는 IP와 Port를 공유한다.

        두개의 컨테이너가 파드가 동일한 경우, localhost를 통해서 통신이 가능하다.

        `A : 8080` ← `B : 7001`  = localhost:8080으로 호출

        `A : 8080` → `B : 7001`  = localhost:7001으로 호출

    - Disk Volume도 공유 가능하다.

        로그 수집기의 경우, 애플리케이션의 로그 파일을 읽어서 수집한다. 파드 내에서 볼륨 공유 특성을 이용하여, 다른 컨테이너 파일을 읽는다.

    - MSA의 SideCar Pattern

        애플리케이션 + 애플리케이션 형식으로 주변 프로그램을 같이 배포한다.

2. **Volume**

    Pod가 가동될 때 컨테이너마다 로컬 디스크를 생성해서 가동한다. local disk는 영구적이지 않기 때문에 유실 우려가 있다. container의 외장 disk 정도로 생각하자. 

    pod가 가동될 때 마운트해서 사용한다.

3. **Service**

    상용 환경에서 일반적으로 여러개의 pod를 서비스하게 된다. Load Balancer를 이용해서 하나의 IP와 Port로 묶어서 서비스를 제공한다.

    Pod는 동적으로 생성되고, 장애가 생길 경우 리스타트되면서 IP가 바뀐다. 그러므로 pod 목록을 지정할 때, IP주소로 관리하는 것이 어렵다. 또한 autoscaling의 경우 pod가 자동으로 추가 / 삭제되기 때문에 pod목록을 로드밸런서가 관리해주어야 한다.

![image](https://user-images.githubusercontent.com/46887352/100063320-64f82480-2e74-11eb-924f-17ae051cc5aa.png)


 Pod에 **label**을 붙일 수 있다. 서비스는 label selector에서 특정 label을 가지고 있는 pod만 선택하여 묶는다.

4. **Namespace**

    한 쿠버네티스 cluster 내의 논리적인 분리 단위. Pod / Service등은 namespace별로 생성 / 관리가 가능하다. 접근 권한이나, 리소스 할당량 등을 나눠서 관리한다.

Label

라벨을 이용해서 리소스를 선택적으로 선택 가능하다.

Controller 

기본 object들을 생성하고, 이를 관리하는 역할을 한다. 

### Replication Controller

Pod를 관리해주는 역할을 한다. 지정된 숫자로 pod를 가동시키고, 관리한다. 

- Selector : label을 기반으로 RC가 관리하는 pod를 가지고 오는데 사용한다.
- Replica 갯수 : 관리되는 pod의 수를 말한다. 그 숫자만큼 pod의 수를 유지하도록 한다.
- Pod Template : pod를 추가로 가동할 때 어떻게 pod를 생성할지, pod에 대한 정보를 이곳에 정의한다.

**`Replica Set`** : Set 기반의 selector를 이용하여 관리한다.

### Deployment

K8s cluster를 구동시키면, 위에 컨테이너화된 애플리케이션을 배포할 수 있다.

→ 이를 위해 deployment 설정을 만들어야한다.

K8s가 애플리케이션의 인스턴스를 어떻게 생성하고, update해야하는지를 지시한다. deployment가 만들어지면 master가 해당 deployment에 포함된 애플리케이션 인스턴스가 클러스터의 개별 노드에서 실행되도록 스케줄한다.


![image](https://user-images.githubusercontent.com/46887352/100063311-60cc0700-2e74-11eb-9a93-922a8acbeef8.png)



### NodePort와 LoadBalancer

- LoadBalancer

    외부 IP를 가지고 있는 LB를 할당한다. 외부 IP를 가지고 있기 때문에, 클러스터 외부에서 접근이 가능하다. LB를 작동시켜 모든 트래픽을 서비스로 forwarding하는 단 하나의 IP주소를 제공한다.

- NodePort

    클러스터 IP로만 접근 가능한 것이 아니라, 모든 노드의 특정 Port를 열어둗고, 이 포트로 들어오는 모든 트래픽을 forward하기 때문에 접근이 가능하다. 서비스에 외부 트래픽을 보낼 수 있는 가장 원시적인 방법이다.

    ex) NodePort type으로 설정하고, `cluster IP = port:80` , `NodePort : 3306` 이라면 둘 다 접근 가능하다.

