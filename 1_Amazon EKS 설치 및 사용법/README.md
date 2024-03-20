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

### 3. Kubernetes Overview

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

### 4. Amazon VPC/ELB/Route 53 Overview

   1) VPC (Virtual Private Cloud)
      -  AWS 클라우드 사용자만의 가상의 프라이빗 클라우드 네트워크를 제공
      -  자신만의 VPC를 생성해 격리된 네트워크 환경을 구성 가능
      -  서브넷을 통해 부분 네트워크로 분리 가능
      -  가상 라우터의 라우팅 테이블을 통해 VPC 내부나 다른 네트워크와 통신할 수 있는 환경 구성 가능
      -  인터넷 게이트웨이나 NAT 게이트웨어 같이 인터넷 구간 통신과 주소 변환을 위한 관문이 되는 서비스 사용 가능
      -  보안그룹 : 인스턴스 레벨의 보안 통신 환경 구성
      -  NACL : 서브넷 레벨의 보안 통신 환경 구성
      -  EKS를 배포하면 컨트롤 플레인과 Data 플레인이 VPC 환경에서 구성
        
   2) ELB (Elastic Load Balancing)
      -  트래픽을 부하분산하며 전달하는 기능
      -  트래픽을 전달할 대상을 지정하는 대상그룹(Target Group)과 어떤 트래픽을 처리할지 정의하는 리스너(Listener)로 구성됨.
      -  ELB는 4가지 유형 : CLB, NLB, ALB, GWLB
      -  아마존 EKS 배포하면 다수의 자원들이 생성되고 확장되거나 축소되는 구조. 다수의 대상에 대해 트래픽을 분산 처리하는 목적에 ELB와 연결되어 동작함
     
   3) Route 53
      -  AWS에서 제공하는 관리형 DNS 서비스
      -  3가지 형태의 동작을 수행
      -  a.도메인 네임을 등록하 등록대행소 역할을 수행 (자신만의 도메인을 구매 가능)
      -  b.생성된 도메인에 대한 권한 있는 네임 서버 역할의 호스팅 영역을 생성
      -  c.생성된 권한 있는 네임 서버에 DNS 레코드를 작성하여 라우팅 정책을 통해 도메인 서비스 제공

---

### 5. Amazon EBS/EFS/S3 Overview

   1) EBS (Elastic Block Storage)
      -  Kubernetes는 컨테이너 스토리지 인터페이스라는 CSI 플러그인을 제공 : AWS 스토리지 서비스와 손쉬운 연결이 가능
      -  EC2 인스턴스에 사용할 수 있도록 네트워크 연결을 통한 블록 스토리지 볼륨을 제공
      -  블록 스토리지의 특성 : 데이터에 빠르게 접근할 수 있고 연구 지속이 가능
      -  결론 : 아마존 EBS는 단일 인스턴스에서 고성능의 접근이 필요한 경우 선택하면 좋음
     
   2) EFS (Elastic File System)
      -  AWS 관리형으로 탄력적인 NFS 파일 시스템을 제공
      -  EFS 파일 시스템을 생성하고 연결하면 사용한 만큼 탄력적으로 확장하는 구조
      -  사실상 용량 제한 없이 사용 가능 (사용한 만큼 비용을 지불)
      -  결론 :  다수의 인스턴스가 공유 스토리지 공간이 필요할 때 사용하면 좋음
        
   3) S3 (Simple Storage Service)
      -  아마존 S3에 저장되는 데이터를 객체라고 하며 객체가 저장되는 곳을 버킷
      -  객체에 대한 입력과 출력은 HTTP 프로토콜을 활용해 REST API로 명령을 전달하여 읽기/쓰기/삭제/수정 수행
      -  웹 기반으로 사용 가능한 객체 기반의 스토리지

---

### 6. AWS IAM Overview

   1) IAM (Identity Access Management)
      -  Identity : 대상을 식별
      -  Access Management : 권한을 통제하여 서비스 접근을 관리
     
   2) IAM 사용자
      -  AWS 자원을 사용하는 객체로 AWS의 계정을 총괄하는 루트 사용자가 아닌 계정 내에 생성된 사용자
      -  IAM 사용자에게 권한을 부여하여 AWS 자원을 통제 가
        
   3) IAM 그룹
      -  다수의 사용자를 집합하여 공통된 작업을 수행

   4) IAM 역할
      -  AWS 자원 사용에 대한 권한이 없는 사용자나 다른 서비스 자체에게 일시적으로 권한을 위임하여 임시 자격 증명을 제공
      -  사용자나 그룹처럼 대상에게 직접적인 권한을 부여하는 것이 아닌 IAM 역할을 부여하여 활용할 수 있음
      -  꼭 사용자가 아니더라도 서비스에게 역할을 부여하여 활용할 수 있음
      -  EKS를 통해 Kubernetes를 관리형으로 동작할 경우 특정 AWS 서비스가 다른 AWS 서비스를 제어하는 경우가 필요 (이때 특정 AWS 서비스에게 IAM 역할을 부여해 사용 권한을 부여해서 제어)
     
   5) IAM 보안주체
      -   IAM 사용자나 역할은 IAM 보안주체라고 분류
      -   보안주체는 특정 작업을 수행할 수 있는 권한이 필요
      -   즉 정책에 의해 요청을 허용할지 거부할지 보안주체가 결정

   6) IAM 정책
      -   자격증명이나 AWS 자원 접근에 대한 권한을 정의
      -   보안주체에게 어떠한 작업을 요청할 때 권한을 평가

---

## Section #2 Amazon EKS 서비스

AGENDA

1. Amazon EKS 소개
2. Amazon EKS Cluster 배포
3. Amazon EKS Control Plane - 아키텍처
4. Amazon EKS Data Plane - 아키텍처
5. Amazon EKS Cluster Endpoint Access

---

### 1. Amazon EKS 소개

   1) Kubernetes 컨트롤 플레인 또는 노드를 제공하는 AWS의 관리형 Kubernetes 서비스
      -  Identity : 대상을 식별
     
   2) 다수의 AWS 가용 영역에 Kubernetes Control Plane 실행
      
   3) 다양한 AWS 서비스와 통합하여 확장성과 보안성 제공
      -  Amazon ECR을 통해 : 컨테이너 이미지 저장소 활용
      -  Amazon ELB을 통해 : 부하 분산 적용
      -  AWS IAM를 통해 : 보안 주체 및 액세스 사용 
      -  Amazon VPC를 통해 : 노드 격리

   4) Kubernetes 최신 버전을 사용하여 Amazon EKS로 손쉬운 마이그레이션
      -  보통 4~5개 마이너 버전을 지원 -> 현재 1.22.1.23, 1.24, 1.25 1.26, 1.27 지원

---

### 2. Amazon EKS Cluster 배포

   1) 관리 콘솔
      -  AWS 관리 콘솔에 접근하여 EKS 클러스터 생성

   2) 관리 콘솔
      -  EKS 클러스터를 생성하고 관리하는 명령어 기반의 CLI 도구

   3) 관리 콘솔
      -  코드기반으로 인프라를 정의해 EKS 클러스터 생성

---

### 3. Amazon EKS Control Plane - 아키텍처

![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/97356961-9305-4035-ab39-98cab0fc1a95)

   1) Kubernetes API 서버, 컨트롤러, 스케줄러, ETCD가 AWS 환경에서 구성
   2) AWS 관리용 VPC 생성 : Amazon EKS 클러스터를 배포하면 컨트롤 플레인을 배치하는 용도
      -  관리용 VPC는 사용자의 어카운트가 아닌 AWS가 자체 관리하는 어카운트에서 생성
      -  다수의 가용 영역을 통해 자원을 배치
      -  ETCD는 데이터 저장소 개념의 컴포넌트로 다른 컴포넌트와 특성이 달라 별도의 인스턴스로 분리하여 구성
   3) 안정적으로 유지하기 위해 오토스케일링 그룹으로 묶어 지속적이고 안정적인 서비스를 유지하도록 구성
   4) 오토스케일링 기능을 통해 지정된 수량의 자원을 유지하거나 확장하거나 축소 가능 (서비스의 연속성)
   5) 가용 영역별로 자원이 다수로 구성됨에 따라 아마존 예의비를 배치해 트래픽을 부하분산하는 환경을 구성
      -  다수의 자원에게 트래픽을 분산하여 효율적인 통신과 고가용성을 보


---

### 4. Amazon EKS Data Plane - 아키텍처



   1) Kubernetes API 서버, 컨트롤러, 스케줄러, ETCD가 AWS 환경에서 구


