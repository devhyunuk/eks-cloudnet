# 1장 Amazon EKS 설치 및 사용

## Section #1 사전지식

AGENDA

1. 가상 머신 vs 컨테이너
2. Docker Overview
3. Kubernetes Overview
4. Amazon VPC/ELB/Route 53 Overview
5. Amazon EBS/EFS/S3 Overview
6. AWS IAM Overview

---

## 3. Kubernetes Overview

   ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/437f186b-5cde-462a-be6a-b7257ebb7392)

   1) Kubernetes라는 뜻이 배의 방향을 조정하는 키를 조작하는 조타수라는 그리스어
      - 도커가 컨테이너를 적지 않은 선박이라면 Kubernetes는 이를 조정하는 조타수의 의미
     
   2) Kubernetes 클러스터는 1개 이상의 노드 구성
      - 노드에는 Kubernetes의 최소 단위인 Pod를 구성할 수 있음
      - Pod내에 컨테이너가 배치되는 구조
      - Kubernetes 클러스터에는 다수의 WorkerNode가 구성되어 컨테이너 환경을 구성
     
   3) 컨트롤 플레인 영역
      - 클러스터 내에 노드를 제어하고 관리하는 목적
      - API Server : 컨트롤 플레인의 프론트엔드 역할을 수행. 사용자가 전달하는 API를 받아 Kubernetes 내부 컴포넌트와 상호작용을 수행
      - Scheduler : 노드에 배정되지 않는 파드를 감지하고 어떤 노드에 실행할지 선택
      - Control Manger : API 서버와 상호작용해 클러스터 내의 컴포넌트 상태를 감지하고 원하는 상태로 전환하는 역할을 수행
      - ETCD : 클러스터 내의 모든 정보를 저장하고 있는 키밸류 형태의 저장소
     
   4) Worker node 영역
      - Kubelet : 컨트롤 플레인과 통신하거나 컨테이너 생성 및 삭제와 상태 모니터링을 수행
      - kube-proxy :  각 노드에서 실행되는 노드의 네트워킹을 책임. kube-proxy를 통해 다른 노드 간의 통신이나 외부 구간의 통신 가
      - container-runtime : 컨테이너가 실행할 수 있는 환경을 구성
        
   5) kubectl 명령
      -  사용자는 kubectl을 통해 API 기반으로 명령을 내릴 수 있음
      -  kubectl 명령을 컨트롤 플레인의 API 서버가 받아 요청에 따른 동작을 수행
      -  컨트롤 플레인 내에 여러 컴포넌트와 상호 작용을 할 수도 있고
      -  쿠블렛에 요청을 전달할 수도 있음

---

## 4. Amazon VPC/ELB/Route 53 Overview

   ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/437f186b-5cde-462a-be6a-b7257ebb7392)

   1) Kubernetes라는 뜻이 배의 방향을 조정하는 키를 조작하는 조타수라는 그리스어
      - 도커가 컨테이너를 적지 않은 선박이라면 Kubernetes는 이를 조정하는 조타수의 의미
