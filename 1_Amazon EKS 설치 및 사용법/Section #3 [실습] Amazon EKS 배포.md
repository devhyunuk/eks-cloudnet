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

      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/187f9ec1-60d9-4e54-871f-11ace8f3a041)

            

      ![image](https://github.com/devhyunuk/eks-cloudnet/assets/49749510/c4fd4f3c-723d-46ca-87ed-7d4725330c71)



















     
