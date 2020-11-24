# Minikube


## Install kubectl

### macOS + curl

1. 최신 릴리즈 다운로드하기

```bash
$ curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl"
```

</br>

2. kubectl 바이너리 실행 가능하게 만들기 

```bash
$ chmod +x ./kubectl
```
</br>

3. 바이너리를 PATH가 설정된 디렉터리로 옮기기

```bash
$ sudo mv ./kubectl /usr/local/bin/kubectl
```

</br>


4. check version

```bash
$ kubectl version --client
```
</br>

위와 같은 화면을 확인 가능합니다.

![image](https://user-images.githubusercontent.com/46887352/100063605-d3d57d80-2e74-11eb-81a7-03e0715b09e7.png)

</br>
</br>

### macOS + homebrew


```bash
### 1. install kubectl with homebrew
$ brew install kubectl
### or
$ brew install kubernetes-cli

### check version
$ kubectl version --client
```
</br>
</br>

### Kubectl

구문

```bash
$ kubectl [command] [TYPE] [NAME] [flags]
```

</br>

**command** : `create`, `get`, `describe` , `delete` 와 같이 하나 이상의 리소스에서 수행하려는 동작을 지정함.

**TYPE** : 리소스 타입을 지정한다. 

**NAME :** 리소스 이름을 지정한다. 이름은 대소문자를 구분함. 이름을 생략할 경우 모든 리소스에 대한 세부사항이 표시.

</br>

kubectl 명령어 / 리소스 info

[kubectl 개요](https://kubernetes.io/ko/docs/reference/kubectl/overview/)

</br>
</br>

### Install minikube with homebrew

+ Install with homebrew

![image](https://user-images.githubusercontent.com/46887352/100063619-d8019b00-2e74-11eb-9b55-b44d1ae98510.png)

</br>

+ start minikube

![image](https://user-images.githubusercontent.com/46887352/100063639-db952200-2e74-11eb-984c-5a41282aa851.png)

</br>

+ minikube dashboard 조회

![image](https://user-images.githubusercontent.com/46887352/100063647-df28a900-2e74-11eb-9224-faeca27029c2.png)

![image](https://user-images.githubusercontent.com/46887352/100063669-e51e8a00-2e74-11eb-8f15-764bb41ac5c1.png)

위와 같은 화면처럼 대시보드 조회 가능합니다.
</br>
</br>

### Minikube + Hello world


+ http server 인 echoserver이미지를 사용해서 쿠버네티스 deployment 만들기

```bash
$ kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.10
```

</br>

![image](https://user-images.githubusercontent.com/46887352/100063676-e8b21100-2e74-11eb-840d-26d506e01a40.png)

</br>

+ `hello-minikube` deployment에 액세스하기 위해 서비스로 노출시켜야 합니다.

```bash
$ kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

![image](https://user-images.githubusercontent.com/46887352/100063690-ebad0180-2e74-11eb-9a88-14257206f62a.png)

</br>
</br>

+ `hello-minikube` pod가 시작되었지만, 노출된 서비스를 통해서 접근하기 전에 파드가 뜨기를 기다려야합니다. 파드가 시작되고 실행중인지 확인합니다.

```bash
$ kubectl get services hello-minikube
```

![image](https://user-images.githubusercontent.com/46887352/100063697-eea7f200-2e74-11eb-8984-5803a61fe747.png)

서비스의 상태를 확인합니다.

</br>
</br>

### Start with —driver = hyperkit

+ driver를 hyperkit으로 설정하여 설치할 경우

```bash
$ minikube start --driver=hyperkit
```

![image](https://user-images.githubusercontent.com/46887352/100064135-7857bf80-2e75-11eb-8233-b65f8048daab.png)

![image](https://user-images.githubusercontent.com/46887352/100064147-7beb4680-2e75-11eb-9d1a-d6c7de5ae2f6.png)

</br>

+ Minikube Start

![image](https://user-images.githubusercontent.com/46887352/100064155-7e4da080-2e75-11eb-918f-35d50f31c1be.png)

</br>

+ 위와 같이 deployment create / expose를 실행합니다.

![image](https://user-images.githubusercontent.com/46887352/100064163-81489100-2e75-11eb-9656-7ec8066babf5.png)

</br>

+ Pod의 Status가 `Running` 임을 확인 후, url을 확인하는 명령어를 실행하여 url을 조회합니다.

```bash
$ kubectl get pod
$ minikube service hello-minikube --url
```

![image](https://user-images.githubusercontent.com/46887352/100064179-84dc1800-2e75-11eb-95c7-c63c41698fe1.png)

</br>
</br>

해당 url을 통하여 접속할 경우 아래와 같은 상태들을 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/46887352/100064187-886f9f00-2e75-11eb-83ff-6bab30be2671.png)

</br>
</br>

**Solution**

driver type을 docker로 설정하면 자꾸 pod를 띄우는 부분에서 에러가 났었는데, **docker version update** 문제였다. 업데이트를 생활화하자..


![image](https://user-images.githubusercontent.com/46887352/100064196-8c9bbc80-2e75-11eb-93d7-c78f4b009db2.png)

![image](https://user-images.githubusercontent.com/46887352/100064205-902f4380-2e75-11eb-872b-9890dc4b8b03.png)

docker driver를 사용해도 디폴로이되고, 위와 같은 페이지로 잘 리다이렉트 되는 것을 볼 수 있습니다.

</br>
</br>

**delete all cluster** 

![image](https://user-images.githubusercontent.com/46887352/100064211-932a3400-2e75-11eb-8540-6fc3b34d35ce.png)


</br>

다음은 kubeadm을 실행시켜보자.
