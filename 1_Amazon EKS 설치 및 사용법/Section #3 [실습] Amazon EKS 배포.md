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

   4) CloudFormation 스택 생성 완료 확인

      #### 1. 스택 확인
      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/9255821d-ad39-45ab-9533-4bcf3e3da2c2)

      #### 2. 리소스 확인
      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/53b12c36-5985-4706-b716-07cb5f7a92c9)

      #### 3. 출력에 eksctlhost 주소 확인
      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/a809f820-0485-4c10-a1da-f2bbb33d2e09)

      #### 4. VPC 확인
      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/95388fb2-b962-4586-969b-17c4eb69bc5c)
         - myeks-VPC에 퍼블릿 서브넷 2개와 프라이빗 서브넷 2개가 생성된 것을 확인
         - 서브넷은 퍼블릿 라우팅 테이블과 프라이빗 라우팅 테이블로 분리
         - 퍼블릭 라우팅 테이블은 인터넷 게이트에 연결

      #### 5. EC2 인스턴스 확인
      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/3a992763-6eb9-427d-9d09-8571577afc4c)



### 3. EKS 관리용 인스턴스 정보 확인

   1). eksctlhost로 접속
      ```
      ssh -i kp_dev_oregon.pem ec2-user@35.93.135.83
      ```
      
   2). 사용자 확인
      ```
      [root@myeks-host ~]# whoami
      root
      ```
      
   3). 기본 설치 도구 확인
   ```
   // kubectl 버전 확인
   [root@myeks-host ~]# kubectl version --client=true -o yaml | yh

clientVersion:
  buildDate: "2023-03-09T20:05:58Z"
  compiler: gc
  gitCommit: bde397a9c9f0d2ca03219bad43a4c2bef90f176f
  gitTreeState: clean
  gitVersion: v1.25.7-eks-a59e1f0
  goVersion: go1.19.6
  major: "1"
  minor: 25+
  platform: linux/amd64
kustomizeVersion: v4.5.7

   // eksctl 버전 확인
   [root@myeks-host ~]# eksctl version
0.174.0

   // awscli 버전 확인
   [root@myeks-host ~]# aws --version
aws-cli/2.15.30 Python/3.11.8 Linux/4.14.336-257.562.amzn2.x86_64 exe/x86_64.amzn.2 prompt/off

   // 도커 정보 확인
   [root@myeks-host ~]# docker info | yh
Server Version: 20.10.25
   ```

   4). awscli 사용을 위한 IAM 자격 증명
   ```
   // awscli로 인스턴스 정보 확인 (IAM 자격 증명 X)
   [root@myeks-host ~]# aws ec2 describe-instances | jq

Unable to locate credentials. You can configure credentials by running "aws configure".

   // IAM 사용자 자격 구성
   aws configure
   ```
   - aws config를 입력하여 Access Key ID, Secret Access Key, Region 코드를 입력
   - IAM 자격 증명이 이루어지면 awscli 도구로 인스턴스 정보 확인

   5). EKS 배포할 VPC 정보 확인
   ```
   // CLUSTER_NAME 변수 확인
   [root@myeks-host ~]# echo $CLUSTER_NAME
myeks

   // EKS를 배포할 myeks-VPC 정보 확인
   aws ec2 describe-vpcs --filters "Name=tag:Name,Values=$CLUSTER_NAME-VPC" | jq

   // EKS를 배포할 myeks-VPC ID 값만 확인
   [root@myeks-host ~]# aws ec2 describe-vpcs --filters "Name=tag:Name,Values=$CLUSTER_NAME-VPC" | jq -r .Vpcs[].VpcId
vpc-0c422ea3c99bbf029
   ```

   6). EKS 배포할 VPC ID 변수 저장
   ```
   // VPCID 변수에 myeks-VPC ID 값을 저장
   [root@myeks-host ~]# export VPCID=$(aws ec2 describe-vpcs --filters "Name=tag:Name,Values=$CLUSTER_NAME-VPC" | jq -r .Vpcs[].VpcId)

   // VPCID를 전역 변수로 선언
   echo "export VPCID=$VPCID" >> /etc/profile

   // VPCID 변수 호출
  [root@myeks-host ~]# echo $VPCID
vpc-0c422ea3c99bbf029
   ```

   7). EKS 배포할 VPC의 서브넷 정보 확인
   ```
   // EKS를 배포할 VPC의 전체 서브넷 정보 확인
   aws ec2 describe-subnets --filters "Name=vpc-id,Values=$VPCID" --output json | jq

   // EKS를 배포할 VPC의 퍼블릭 서브넷 정보 확인
   aws ec2 describe-subnets --filters Name=tag:Name,Values="$CLUSTER_NAME-PublicSubnet1" | jq

   aws ec2 describe-subnets --filters Name=tag:Name,Values="$CLUSTER_NAME-PublicSubnet2" | jq

   // EKS를 배포할 VPC의 퍼블릭 서브넷 ID 값만 확인
   [root@myeks-host ~]# aws ec2 describe-subnets --filters Name=tag:Name,Values="$CLUSTER_NAME-PublicSubnet1" --query "Subnets[0].[SubnetId]" --output text
subnet-031bf8002dd9e2087

   [root@myeks-host ~]# aws ec2 describe-subnets --filters Name=tag:Name,Values="$CLUSTER_NAME-PublicSubnet2" --query "Subnets[0].[SubnetId]" --output text
subnet-008213cb8bc558678
   ```

   8). EKS 배포할 퍼블릭 서브넷 ID 변수 저장
   ```
   // 변수에 퍼블릭 서브넷 ID 값을 저장
   export PubSubnet1=$(aws ec2 describe-subnets --filters Name=tag:Name,Values="$CLUSTER_NAME-PublicSubnet1" --query "Subnets[0].[SubnetId]" --output text)

   export PubSubnet2=$(aws ec2 describe-subnets --filters Name=tag:Name,Values="$CLUSTER_NAME-PublicSubnet2" --query "Subnets[0].[SubnetId]" --output text)

   // 퍼블릭 서브넷 ID를 전역 변수로 선언
   echo "export PubSubnet1=$PubSubnet1" >> /etc/profile

   echo "export PubSubnet2=$PubSubnet2" >> /etc/profile

   // VPCID 변수 호출
   [root@myeks-host ~]# echo $PubSubnet1
subnet-031bf8002dd9e2087

   [root@myeks-host ~]# echo $PubSubnet2
subnet-008213cb8bc558678
   ```

   9). 변수 호출 (종합)
   ```
   [root@myeks-host ~]# echo $AWS_DEFAULT_REGION
us-west-2

   [root@myeks-host ~]# echo $CLUSTER_NAME
myeks

   [root@myeks-host ~]# echo $VPCID
vpc-0c422ea3c99bbf029

   [root@myeks-host ~]# echo $PubSubnet1,$PubSubnet2
subnet-031bf8002dd9e2087,subnet-008213cb8bc558678
   ```

---

### 3. CloudFormation으로 기본 인프라 배포

   1) 스택 생성
      - cnaeb_ch1_lab_1.yaml 베이스로 생성





     
