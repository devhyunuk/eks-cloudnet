# Section #3 [실습] Amazon EKS 배포

## AGENDA

1. 실습 개요
2. CloudFormation으로 기본 인프라 배포
3. 관리 콘솔에서 Amazon EKS 배포
4. eksctl에서 Amazon EKS 배포
5. 실습 환경 삭제

---

### 1. 실습 개요

   ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/87d8b50f-6c36-48b1-a043-4dc88bdd2783)

---

### 2. CloudFormation으로 기본 인프라 배포

   1) 스택 생성
      - cnaeb_ch1_lab_1.yaml 베이스로 생성
      
      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/0c2bc5b1-0819-40ed-a5d2-668fb566d8c5)
   
   2) 스택 세부 정보 지정
      - KeyName과 SgIngressSshCidr 지정
        
      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/56031881-bfa4-4e35-b6e3-356206fd73d5)


   3) CloudFormation으로 기본 인프라 배포 구성

      #### 1. 기본 인프라 배포 구성도
      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/187f9ec1-60d9-4e54-871f-11ace8f3a041)

      - MyEKS VPC가 생성
      - 2개의 가용 영역에 Public Subnet 2개, Private Subnet 2개가 생성
      - Public Subnet과 Private Subnet은 각각의 라우팅 테이블이 별도로 구성
      - Public Subnet의 인터넷 구간 통신을 위해 인터넷 게이터에도 구성
      - MyEKS 호스트라는 EC2 인스턴스가 퍼블릭 서브넷에 구성 -> EKS 관리용도의 인스턴스 (Bastion Host 역할)
      - 보안 그룹은 앞서 설정에 따라 각자 PC만 접근이 가능한 보안 정책 (SgIngressSshCidr 지정)

      #### 2. 기본 인프라 배포 정보

      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/c4fd4f3c-723d-46ca-87ed-7d4725330c71)
      
      - 클라우드 포메이션으로 생성되는 기본 인프라 정보
      - 192.168.0.0의 16비트의 MyEKS VPC 생성
      - 퍼블릭 서브넷 2개, 프라이빗 서브넷 2개가 생성
      - 2개의 가용 영역이 생성 (IP사이더는 분리)
      - 외부 구간 통신을 위한 인터넷 게이트가 생성
      - 퍼블릭 용도와 프라이빗 용도에 따른 라우팅 테이블도 별도로 생성
      - MyEKS 인스턴스가 퍼블릭 서브넷에 위치하여 외부 인터넷 구간과 통신이 가능
      - EKS 관리를 위해 kubectl, eksctl, awcli, docker, kubernetes 플러그인 등이 설치
      - EKS 호스트의 접근 제어를 위해 보안 그룹이 연결



















     
