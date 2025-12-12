# 컨테이너는 애플리케이션과 그 실행환경 전체를 하나로 묶어 만든 실행 패키지 ===> 앱이 어디서든 똑같이 실행되도록 캡슐에 넣어버리는 기술. 환경 차이 0% → 이식성 + 안정성 폭증

# 컨테이너는 가볍다
기존 VM(가상머신) : OS 전체를 통째로 하나 더 올림, 느림, 무거움, 용량 큼
컨테이너 : OS는 공유, 필요한 라이브러리만 포함, 이미지 용량 작음, 실행 속도 빠름, 수십~수백 개도 한 서버에 띄움 가능

# 컨테이너의 이미지 = 애플리케이션 코드, 라이브러리, 의존성(dependency), 환경변수/설정파일, 파일 구조 포함됨

===============================================================

# kubectl 이란?  // 큐-브-컨-트롤
= 쿠버네티스(Kubernetes) 클러스터를 조작하는 명령어 도구(CLI)

# 명령어 구조
kubectl [COMMAND] [TYPE] [NAME] [flags...]

| COMMAND | 무엇을 할지 (동작) | get, create, delete, apply |
| TYPE    | 어떤 리소스 대상   | pod, deployment, service   |
| NAME    | 대상 이름       | my-pod, nginx-dep          |
| flags   | 옵션          | -o yaml, --namespace dev   |

kubectl get pod my-pod -o yaml
==> my-pod라는 pod를 YAML 형식으로 보여줘  //야멀

# 기본 동작 명령어
-1) 생성/배포(Create/Apply)
  Deployment 생성 예시
kubectl create deployment my-dep --image=nginx  //엔-진-엑스
==> nginx 이미지를 사용하는 my-dep 라는 Deployment를 새로 만든다

  YAML 파일을 통해 리소스 생성 또는 업데이트
kubectl apply -f <파일명.yaml>

-2) 조회(Get)
  Pod 목록 조회
kubectl get pods
==> 현재 클러스터의 Pod의 상태(Running, Pending 등)를, 이름, Ready 확인가능

  Deployment 목록 조회
kubectl get deployments
==> replicas, ready, up-to-date 등 확인 가능

-3) 상세 정보 확인(Describe)
  리소스의 이벤트나 설정, 에러 메시지 등을 상세히 볼 수 있음
kubectl describe [TYPE] [NAME]

예시) kubectl describe pod my-pod
==> 리스타트 횟수, Node 상태등등을 알수있어서 문제 해결할 때 가장 많이 사용하는 명령

-4) 삭제(Delete)
  Pod 삭제
kubectl delete pod my-pod
==> 지우면 Deployment가 다시 새 Pod를 만들 수 있음

-5) 업데이트(Edit/Scale)
  리소스 정의(YAML)를 직접 편집
kubectl edit [TYPE] [NAME]
==> 리소스(YAML)를 직접 vi 에디터로 열어서 즉시 수정한다.

  Deployment 스케일 업/다운
kubectl scale deployment [NAME] --replicas=[숫자]

  출력 예시
Client Version: version.Info{Major:"1", Minor:"XX", GitVersion:"v1.XX.X", ...}      ===> 내 컴퓨터에 설치된 kubectl의 버전
Server Version: version.Info{Major:"1", Minor:"XX", ...}          ====> 현재 연결된 Kubernetes 클러스터의 버전

kubectl 버전(Client)과 서버 버전의 차이가 너무 크면 비호환성이 커지기 때문에 클러스터 관리 시 업그레이드 여부 판단할 필요가 있기 떄문에 둘다 봄

  노드 목록 조회와 그 예시
kubectl get nodes

NAME             STATUS   ROLES                  AGE   VERSION
docker-desktop   Ready    control-plane,master   10m   v1.XX.X        ====> Ready가 되면 정상 작동 됨


## Deployment

# 생성
kubectl create deployment hello-kubectl --image=nginx

# nginx 이미지는 어디서 오나요?
Pod 생성 → nginx 이미지 필요
로컬에 없나 확인 → 없음
기본 레지스트리 = Docker Hub
Docker Hub에서 nginx:latest 다운로드(Pull)
컨테이너 생성해서 Pod 실행


# Deployment 목록 확인
kubectl get deployments
===> "hello-kubectl"이라는 Deployment가 표시되며, READY 항목이 1/1이면 nginx 컨테이너가 제대로 뜬 것

# Pod 목록 확인
kubectl get pods
====> hello-kubectl-<난수> 형태의 파드가 1개 실행 중이어야 함. STATUS가 Running이면 정상

[사진]

# Deployment 상세 정보 확인

kubectl describe deployment hello-kubectl
Pod이 언제 어떻게 만들어졌는지, 어떤 이미지를 쓰고 있는지, 이벤트 내역은 없는지 등 상세 정보를 볼 수있다

# Deployment 삭제

kubectl delete deployment hello-kubectl
"deployment.apps "hello-kubectl" deleted" 메시지가 나오면 성공입니다.


# nginx 브라우저로 확인하기
  만약 방금 삭제했다면 다시 생성
kubectl create deployment hello-kubectl --image=nginx

  파드 이름 확인
kubectl get pods

  포트 포워딩
kubectl port-forward <파드이름> 38080:80           
=====> 이 명령에서 http://localhost:38080 로 접속하면, "Welcome to nginx!" 문구가 표시됩니다.

포트포워딩의 특징:
일시적이다 → 터미널 종료(Ctrl+C)하면 접속 불가됨.
빠르게 Pod 안 서비스가 정상인지 확인할 때 사용.

# 이미 hello-kubectl 배포가 있다면, 그 위에 Service만 추가
kubectl expose deployment hello-kubectl --type=NodePort --port=80 --target-port=80

| `--type=NodePort`  | 외부에서 접속 가능한 NodePort 타입 Service 생성 |
| `--port=80`        | Service가 제공하는 포트(클러스터 내부에서 접근 시)   |
| `--target-port=80` | 실제 Pod 안에서 nginx가 동작하는 포트          |

# 서비스 확인
kubectl get svc

NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
hello-kubectl   NodePort    10.98.224.219   <none>        80:31770/TCP   4s

=====> 
| hello-kubectl | Service 이름               |
| NodePort      | 외부에서 접속 가능한 타입           |
| 10.98.224.219 | 클러스터 내부에서 접근하는 IP        |
| <none>        | 외부 공개용 IP 없음             |
| 80:31770/TCP  | 내부는 80, 외부는 31770 포트로 접근 |
| 4s            | 생성된 지 4초                 |


## pod : 포장지에 있는 컨테이너

##Pod은 일회성으로 취급됩니다. 즉, Pod이 문제가 생겨 종료되면 자동으로 다시 살려야 하는데, 이를 Pod 단독으로 관리하기에는 불편하기 때문에 Deployment 같은 상위 오브젝트로 Pod을 관리하는 것이 일반적입니다.
==> Pod은 쉽게 죽고 일회성이라 직접 관리하기 불편하므로, 보통 Deployment를 만들어 Pod을 자동으로 관리한다

## Deployment
Deployment는 Pod를 생성하고 운영(배포, 롤링 업데이트, 스케일링 등)을 자동으로 관리하는 쿠버네티스 리소스입니다. ===> pod를 관리하는! (쿠버네티스가 해줌)

## yml 설정
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-deployment
spec:
  replicas: 1      ====> pod를 1개 실행하라는 설정
  selector:        ====> 관리할 pod가 어디인지 찾아내는 부분 (라벨을 붙여줌)
    matchLabels:
      app: hello-nginx  ===> hello-nginx가 있는 모든 pod를 관리할 예정
  template:
    metadata:
      labels:
        app: hello-nginx    
    spec:
      containers:
      - name: nginx-container
        image: nginx:alpine
        ports:
        - containerPort: 80  =====> 이 컨테이너가 내부에서 사용하는 포트는 80번 (nginx 기본 포트)
