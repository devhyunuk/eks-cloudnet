# 1장 Amazon EKS 설치 및 사용

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
      -  다수의 자원에게 트래픽을 분산하여 효율적인 통신과 고가용성을 보장.


---

### 4-1. Amazon EKS Data Plane - 아키텍처

![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/88431e17-fc0f-4e57-b51c-8242aacda0e6)

   1) EKS 데이터 플레인 : Kubernetes 클러스터의 노드를 구성하기 위해 데이터 플레인 컴포넌트를 구성하는 영역
   2) kubelet, kube-proxy, container-runtime, pod 구성
   3) 컨트롤 플레인과 다르게 사용자 VPC에서 생성 (사용자가 직접적으로 관리 및 운용이 가능)
   4) kubelet은 컨트롤 플레인에 위치한 API 서버와 통신을 위해 연결 필요
   5) 컨트롤 플레인과 데이터 플레인은 EKS Owned ENI라는 네트워크 인터페이스로 연결
   6) 컨트롤 플레인과 데이터 플레인은 EKS-owned ENI를 통해 연결

---

### 4-2. Amazon EKS Data Plane - 노드를 구성하는 방식 3가지

   1) 관리형 노드 그룹
      - EKS에 최적화된 최신 AMI를 사용
      - 유지관리 및 버전 관리를 AWS에서 지원
      - 관리 부하 : 보통
      - 제약 사항 : 보통
        
   2) 자체 관리형 노드
      - 사용자 정의 AMI를 사용
      - 유지 관리 및 버전 관리를 직접 수행
      - 관리 부하 : 높음
      - 제약 사항 : 적음
        
   3) AWS Fargate
      - 사용자가 직접 관리 없이 서버리스 환경에서 동작
      - AWS Fargate 환경에서 제공하는 Micro VM 형태로 할당하여 관리
      - 관리 부하 : 낮음
      - 제약 사항 : 많음

---

### 5. Amazon EKS Cluster Endpoint Access - Public

   1) Endpoint Access - Public 방식

      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/c933dd33-3dcb-4599-adcc-7c1c242d063e)

      - API 서버로 접근을 위해 엔드포인트를 정의
      - 엔드포인트 방식을 퍼블릭으로 구성하면 : 엔드포인트를 접근하는 도메인 주소를 퍼블릭 IP 주소로 맵핑
      - 퍼블릭 IP 주소는 NLB의 퍼블릭 IP를 정의
      - Public 방식에 따른 트래픽 흐름 3가지
        - A. 컨트롤 플레인의 API 서버에서 -> 워커노드의 kubelet으로 전달하는 트래픽 흐름
          - API 서버에서 EKS-owned ENI를 통해 워커노드의 쿠블렛으로 전달
        - B. kubelet에서 -> api 서버로 전달하는 트래픽 흐름
          - 엔드포인트가 public ip 주소이기 때문에 인터넷 게이트 위로 빠져나가 인터넷 구간에 통해 관리형 vpc로 진입하고 api 서버가 전달받음
          - 외부 인터넷 구간으로 트래픽이 노출되어 보안에 위협
        - C. kubectl의 명령을 통해 -> API 서버로 전달하는 트래픽 흐름
          - 외부 인터넷 구간에서 관리형 VPC로 진입하고 API 서버가 전달을 받음
          - 퍼블릭 환경임에 따라 사용자는 외부 구간에서 인터넷을 통해 자유롭게 접근이 가능 (보안에 위협)
            

   2) Endpoint Access - Public + Private 방식

      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/8da98642-594f-4abe-a15b-df86bb95f968)

      - 엔드포인트의 도메인 주소는 사용자를 위한 주소로 public IP 주소로 정의
      - 워커노드를 위한 주소는 별도의 프라이빗 호스팅존을 구성해 대상을 EKS-owned-ENI로 구성 (Public 방식과 차이점)
      - Public + Private 방식에 따른 트래픽 흐름 3가지
        - A. 컨트롤 플레인의 API 서버에서 -> 워커노드의 kubelet으로 전달하는 트래픽 흐름
          - API 서버에서 EKS-owned ENI를 통해 워커노드의 kubelet으로 전달
        - B. kubelet에서 -> api 서버로 향하는 트래픽 흐름
          - AWS 내부의 Private Hosting Zone에게 DNS 질의를 한 후
          - 대상을 EKS Owned ENI로 전달 ->  API 서버에 전달
          - 외부 인터넷 구간에 노출 없이 보완성 있는 통신
        - C. 사용자에서 kubectl 명령을 API 서버에게 전달 트래픽 흐름
          - 맵핑된 도메인 주소에 따라 Public IP로 API 서버가 전달

   3) Endpoint Access - Private 방식

      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/43ccfd44-20b9-4545-93d9-7d438f64efcd)
      
      - 엔드포인트 방식이 오직 프라이빗이기 때문에 접근할 엔드포인트 도메인 주소는 AWS 내부의 프라이빗 호스팅 존에서만 관리
      - 사용자는 커스텀 VPC에 위치해서 kubectl 명령을 수행만 가능 (내부에서만 가능)
      - 일반적인 비즈니스 환경에서 EKS 클러스터 엔드포인트 Private 방식을 선호
        - A. 컨트롤 플레인의 API 서버에서 -> 워커노드의 kubelet으로 전달하는 트래픽 흐름
          - API 서버에서 EKS-owned ENI를 통해 워커노드의 kubelet으로 전달
        - B. kubelet에서 -> api 서버로 향하는 트래픽 흐름
          - AWS 내부의 Private Hosting Zone에게 DNS 질의를 한 후
          - 대상을 EKS Owned ENI로 전달 ->  API 서버에 전달
          - 외부 인터넷 구간에 노출 없이 보완성 있는 통신
        - C. 사용자에서 kubectl 명령을 API 서버에게 전달 트래픽 흐름
          - Private Hosting Zone에 의해 EKS-owned ENI를 통해 API 서버로 통신
          - 외부 인터넷 구간의 노출 없이 AWS 내부로 동작하여 보완성 있는 통신과 효율적인 통신
