# 🏬 EZPZ
![Frame 3](https://github.com/user-attachments/assets/9db7d2ef-fa15-402b-a514-5e0ef5e414d7)

**EZPZ**는 `'Easy Popup Zone'`의 약자로, 사용자들에게 간편하고 직관적인 팝업 스토어 경험을 제공하는 것을 목표로 합니다. EZPZ는 최신 팝업 트렌드를 한눈에 볼 수 있는 허브 역할을 하며, 사용자가 쉽고 빠르게 예약하고 원하는 굿즈를 구매할 수 있도록 돕습니다. EZPZ를 통해 팝업 스토어의 모든 과정을 손쉽게 관리하고 즐길 수 있는 최적의 공간을 제공하여, 트렌드에 민감한 MZ 세대의 기대를 충족시키고자 합니다.
</br></br>

## ✔️ ERD
![Copy of ezpz (최종) (1)](https://github.com/user-attachments/assets/56b9f919-304d-4c42-8641-34949d46fa30)

## ✔️ 요구사항 명세
https://docs.google.com/spreadsheets/d/1_3SPLxWsbA3cSi7pJJ3N0nBEA1QJgEhzCbwG_eMMU6c/edit?usp=sharing
</br></br>


## ✔️ API 명세
https://docs.google.com/spreadsheets/d/1idzqXzU3zV-MfIImZvRcLJuWeOOjaQd8rtfax7csXdI/edit?usp=sharing
</br></br>

## ✔️ 서비스 아키텍처
![인프라구성도 drawio](https://github.com/user-attachments/assets/8f3117ea-06b0-459b-875b-551671996083)

## ✔️ GitHub 주소
### Backend
- User : https://github.com/NBCAMP-EZPZ/EZPZ-User
- Host : https://github.com/NBCAMP-EZPZ/EZPZ-Host
- Admin : https://github.com/NBCAMP-EZPZ/EZPZ-Admin

### FrontEnd
- User : https://github.com/NBCAMP-EZPZ/EZPZ-User-Front
- Host : https://github.com/NBCAMP-EZPZ/EZPZ-Host-Front
- Admin : https://github.com/NBCAMP-EZPZ/EZPZ-Admin-Front

</br></br>

## ✔️ 주요 기술
|기술|사용처|
|:-:|:-:|
|AWS S3|이미지 파일 저장소 및 스크립트 파일 저장소|
|AWS EC2|프로젝트 배포를 위해서 클라우드 컴퓨팅 서비스|
|AWS ElastiCache|Redis를 사용하여, Refresh 토큰 저장, 캐시 처리, 분산락 처리|
|AWS RDS|MySQL을 사용하며, 자동화된 관리와 백업을 통해 운영 부담 줄임|
|GitHub Action|자동화된 파이프라인을 통해서 코드 변경과 배포를 원활하게 하기 위해서 CI/CD 적용|
|Docker|JAVA 파일을 빌드 및 배포|
|AWS ECR|Docker 이미지의 안전한 관리를 지원|
|AWS CodeDeploy|EC2 인스턴스, ECS, serverless lambda function 등 여러 서비스 애플리케이션 배포를 자동화|
|AWS Route53|외부의 프론트엔드 어플리케이션으로 라우팅 및 ALB 인프라로 요청 전달|
|AWS ALB|어플리케이션의 트래픽을 분산, 클라이언트 요청에 따른 서버 라우팅|
|Spring Batch|대량의 통계 데이터 처리|
|Netlify|프론트엔드 어플리케이션 배포|
|nGrinder|Traffic을 던져 성능 테스트|

## ✔️ 주요 기능
### 👤 User
- 팝업 목록 조회
- 팝업 상세 조회
- 팝업 굿즈 목록 조회
- 팝업 굿즈 상세 조회
- 굿즈 장바구니 담기
- 굿즈 주문
- 팝업 오프라인 예약 목록 조회
- 팝업 오프라인 예약 상세 조회
- 팝업 오프라인 예약 등록
- 팝업, 굿즈 좋아요
- 좋아요 목록 조회
- 쿠폰 목록 조회
- 쿠폰 선착순 다운로드

### 🏢 Host
- 팝업 목록 조회
- 팝업 등록
- 팝업 굿즈 등록
- 팝업 굿즈 판매량 통계 조회
- 팝업 오프라인 예약 목록 조회
- 팝업 오프라인 예약 상세 조회

### 🧑‍💻 Admin
- 팝업 목록 조회
- 신규 팝업 심사
- 쿠폰 발행

## ✔️ 기술적 의사 선택
<details>
  <summary>1. 동시성 제어</summary>
  
  > 도입 배경

  데이터베이스 접근을 필요로 하는 요청(예: 선착순 쿠폰 다운로드)들이 동시에 들어올 때 트랜잭션 단위로 데이터베이스에 접근하게 되는데, 이를 동시적으로 모두 허용해주면 레이스 컨디션이 발생하여 데이터베이스의 일관성과 무결성이 깨지게 됩니다. 이를 방지하기 위해 동시성 제어를 통해 데이터베이스를 보호해야 했습니다.

  > 선택지

  **낙관적 락**은 데이터 충돌이 발생했을 때 이를 해결하기 위한 추가적인 로직(재시도 로직)이 필요하고, 충돌이 자주 발생하는 시스템에서는 이를 해결하기 위한 추가적인 시간이 필요합니다. </br>
  **비관적 락**은 동시 접속자가 많은 환경에서 락 대기 시간으로 인해 성능에 영향을 줄 수 있고, 다수의 트랜잭션이 서로 다른 순서로 여러 데이터에 락을 요청하면 데드락이 발생할 수 있습니다.</br>
  **분산락**은 서버 분산 환경에서도 프로세스들의 원자적 연산이 가능하지만, 락 서비스를 설정하고 관리하는 것이 복잡합니다.

  > 의사결정

  서버 분산 환경에서도 일관성을 유지해야 하기 때문에 분산락을 사용하기로 결정했습니다.

</details>
