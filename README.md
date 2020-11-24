# Container Orchestration

[🐳 Kubernetes?](https://github.com/kimyuuum/kubernetes-minikube/blob/main/kubernetes.md)

[🐳 Minikube Tutorial 수행하기](https://github.com/kimyuuum/kubernetes-minikube/blob/main/minikube-tutorial.md)

# Overview

# 1. Container

컨테이너는 모듈화되고, 격리된 컴퓨팅 공간 및 환경이며, 애플리케이션 구동을 위해 격리한다. 각 user마다 다른 개발 환경을 가지고 있을 수 있는데, 이에 종속되지 않고 애플리케이션을 동작 및 배포하기 위하여 container를 사용한다.

**가상화**는 단일 HW System에서 여러 OS가 동시에 실행 가능하게 한다. 기존에는 하이퍼바이저, Guest OS가 필요했다. 

하지만 **컨테이너**는 동일한 OS 커널을 공유하고, System의 나머지 부분으로부터 애플리케이션 프로세스를 격리한다. 애플리케이션 실행에 필요한 모든 파일만을 패키징한다. 이러한 특성을 기반으로 컨테이너가 가볍고 빠르다는 장점을 가지고 있다.

**OS level 가상화** 

: os의 kernel이 여러 격리된 사용자 공간 instance를 갖출 수 있도록 한다.

**컨테이너의 특징**

- container 이미지를 이용한 배포와 롤백이 간단하다.
- 언어나 framework에 상관없이 애플리케이션을 동일한 방식으로 관리한다
- 개발 / testing / 운영 환경 등 local PC와 클라우드까지 동일한 환경을 구축할 수 있게 한다.

컨테이너의 생성은 OS에서 보면 단순하게 process를 시작하는 것. → 매우 빠른편이다

다양한 Service들을 컨테이너로 만든다 → **Containerization**

**이미지**는 실행 파일의 개념이며, **컨테이너**는 프로세스의 개념과 유사하다. 

**System Container**

컨테이너 기술들을 사용해 OS위에 HW가상화 없이 운영체제를 실행한다. 일반 linux처럼 init process 등을 사용해서 다수의 process가 같은 환경을 공유하는것이 목표이다.

→ LXC

**Application Container**

컨테이너 기술을 활용하여 하나의 애플리케이션 ( 프로세스 ) 를 실행하는 것을 목표로 한다. 독립적인 환경이라는 점이 유사하지만, 단 하나의 프로세스만 실행한다는 점에서 확장이 쉽고, 관리 요소가 거의 없다.

→ Docker

**Issues**

1. 커널 공유

    컨테이너 가상화는 기존 방법보다 클라우드 구성에 있어서 효율적이지만, 단점도 존재한다. 컨테이너는 host OS의 통제영역을 사용한다 하더라도, 많은 컨테이너들이 동일 OS커널을 공유하게 된다. → 철저하게 분리되어있지는 않아서, 안정성 측면의 issue가 존재한다.

    → 안전 장치가 있긴 하지만 동일 host이기 때문에 scheduling이나 예기치 못한 컨테이너간의 충돌 우려도 존재한다.

2. 영속성

    기본적으로 컨테이너는 비저장성을 갖는 이미지로부터 실행한다. 컨테이너가 새 프로세스를 실행하면, 컨테이너는 사라지고 재시작된다. 신규 컨테이너는 이전 컨테이너와 연결된 상태 정보가 없다.

    → 이전 세션작업을 포하마하는 새로운 이미지 생성이 필요하다.

    이를 위해 c**ontainer와 연결된 별도의 DB나 독립적인 스토리지가 있어야 한다**. 가상 머신은 자체 파일 시스템으로 세션에 대한 영속성을 가지고 있다.

# 2. Orchestration

### **서버 관리**

서버의 상태를 관리하기 위한 노력으로 문서관리, 서버 설정 관리가 있는데, 이로부터 발생하는 기본적인 복잡함이 발생했다. 너무 많은 컨테이너를 관리하기는 번거롭다. 

예를 들어, Container를 배포해야하는 상황이 있다고 가정하자. Server 3개가 있을 경우, 각각 ssh로 접속해서 관리해야한다. **이러한 복잡한 컨테이너 환경을 효과적으로 관리하기 위한 솔루션**으로 나온것이 container orchestration이다. 

컨테이너를 사용하는 어떤 환경에서도 사용 가능하다. **마이크로서비스 기반 애플리케이션**에 이상적이다.

### **특징**

1. Cluster

    여러개의 node를 클러스터 단위로 추상화하여 관리한다. master server에서 node에 명령을 각각 전송한다. 클러스터로 관리할 경우 node scale을 고려해야 할 필요가 있다.

2. State

    image에 대하여 직접 조치를 하지 않아도, replica와 같은 관리를 진행한다.

3. Scaling

    Load Balancing

    생성된 컨테이너의 컴퓨팅 자원 사용량의 설정 및 자동 배분을 진행함

    Scheduling

    늘어난 컨테이너를 적합한 서버에 나누어 배포하고, 서버가 다운될경우 실행중인 컨테이너를 다른 서버에서 구동시킨다. 서버 간 여유가 있는지에 대한 관리를 진행한다.

4. Deploy / Version Control

    배포, 버전 관리를 중앙에서 할 수 있게끔 한다.

5. Service Discovery

    서비스 탐색 기능으로 기본적으로는 클라우드 환경에서 컨테이너의 생성과 배치, 이동 여부를 알 수 없기 때문에 IP Port, 정보 업데이트 및 관리를 통해 서비스를 지원한다. 여러 서버를 조회하거나 watch할때 설정 변경 / 재시작을 자동화한다.

6. Logging / Monitoring

    여러개의 서버를 동시에 관리할 경우, 한곳에서 서버 상태를 모니터링하고 로그 관리를 할 수 있게 한다.
