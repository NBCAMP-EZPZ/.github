# 🏬 EZPZ
![Frame 3](https://github.com/user-attachments/assets/9db7d2ef-fa15-402b-a514-5e0ef5e414d7)

**EZPZ**는 `'Easy Popup Zone'`의 약자로, 사용자들에게 간편하고 직관적인 팝업 스토어 경험을 제공하는 것을 목표로 합니다. EZPZ는 최신 팝업 트렌드를 한눈에 볼 수 있는 허브 역할을 하며, 사용자가 쉽고 빠르게 예약하고 원하는 굿즈를 구매할 수 있도록 돕습니다. EZPZ를 통해 팝업 스토어의 모든 과정을 손쉽게 관리하고 즐길 수 있는 최적의 공간을 제공하여, 트렌드에 민감한 MZ 세대의 기대를 충족시키고자 합니다.
</br></br>

## ✔️ 서비스 아키텍처
![인프라구성도 drawio](https://github.com/user-attachments/assets/8f3117ea-06b0-459b-875b-551671996083)
</br></br>

## ✔️ ERD
![Copy of ezpz (최종) (1)](https://github.com/user-attachments/assets/56b9f919-304d-4c42-8641-34949d46fa30)

## ✔️ 요구사항 명세
https://docs.google.com/spreadsheets/d/1_3SPLxWsbA3cSi7pJJ3N0nBEA1QJgEhzCbwG_eMMU6c/edit?usp=sharing
</br></br>


## ✔️ API 명세
https://docs.google.com/spreadsheets/d/1idzqXzU3zV-MfIImZvRcLJuWeOOjaQd8rtfax7csXdI/edit?usp=sharing
</br></br>

## ✔️ GitHub 주소
### Backend
- User : https://github.com/NBCAMP-EZPZ/EZPZ-User
- Host : https://github.com/NBCAMP-EZPZ/EZPZ-Host
- Admin : https://github.com/NBCAMP-EZPZ/EZPZ-Admin

### FrontEnd
- User : https://github.com/NBCAMP-EZPZ/EZPZ-User-Front
- Host : https://github.com/NBCAMP-EZPZ/EZPZ-Host-Front
- Admin : https://github.com/NBCAMP-EZPZ/EZPZ-Admin-Front

## ✔️ 시연 영상
https://youtu.be/tc06RjScPmA
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

</br></br>

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

</br>

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
<details>
  <summary>2. 분산락 구현</summary>
  
  > 도입 배경

  동시성 제어를 하는 방법으로 분산락을 이용하기로 결정했기 때문에 분산락을 구현해야 했습니다.

  > 선택지

  **Zookeeper**는 분산 시스템에서 높은 일관성과 동기화 기능을 제공합니다. 데이터가 디스크에 저장되므로 시스템 장애 시에도 데이터 일관성을 유지할 수 있고, 단순한 락 외에도 분산 환경에서 필요한 다양한 코디네이션 기능을 제공합니다. 하지만 클러스터를 설정하고 관리하는 것이 복잡하고, 네트워크 지연이나 클러스터의 크기에 따라 성능이 저하될 수 있습니다. </br>

**MySQL**은 기존의 데이터베이스 인프라를 활용하여 추가적인 비용 없이 락 기능을 구현할 수 있습니다. SQL 쿼리로 간단히 락을 구현할 수 있으며, 데이터베이스 트랜잭션을 활용하여 락을 처리할 수 있습니다. 하지만 성능이 저하될 수 있으며, 여러 트랜잭션이 동시에 락을 요청할 때 경합이 발생할 수 있습니다. </br>

**Redis**는 인메모리 기반의 데이터 저장소로 매우 빠른 성능을 제공하며, TTL(만료 시간)을 설정하여 자동으로 락을 해제할 수 있습니다. 또한 Redis의 간단한 API를 사용하여 분산락을 쉽게 관리할 수 있습니다. 하지만 Redis 인스턴스가 장애를 겪으면 락 기능에 영향을 줄 수 있으며, 데이터가 손실될 수 있습니다.

  > 의사결정

  락의 성능이 좋고, Redis 라이브러리에서 다양한 분산락 구현체들을 지원하기 때문에 손쉽게 사용이 가능할 뿐만 아니라, 캐시 처리와 토큰 저장 등에도 Redis가 같이 쓰이기 때문에 Redis를 이용하여 분산락을 구현하기로 결정했습니다.</details>
<details>
  <summary>3. Redisson</summary>
  
  > 도입 배경

  분산락을 구현하는 방법으로 Redis을 이용하기로 결정했었습니다. Redis를 이용한 분산락을 구현하기 위해서 구현체를 선택해야 했습니다.
  
> 선택지

  **Jedis**는 Java에서 Redis와 상호작용하기 위한 기본적인 클라이언트 라이브러리입니다. 사용이 간편하고 Redis 명령어와 직접 상호작용할 수 있어 구현이 용이합니다. 하지만 Redis 클러스터와 같은 고급 기능을 사용하려면 추가 설정이 필요할 수 있고, 기본적으로 동기 API를 제공하기 때문에, 비동기 작업은 별도의 라이브러리가 필요할 수 있습니다.</br>

  **Lettuce**은 spring-data-redis의 기본 구현체로, 비동기, 반응형(reactive) 및 클러스터 지원을 제공합니다. 또한 멀티플렉싱을 지원하여 여러 Redis 커넥션을 효율적으로 관리할 수 있습니다. 이를 통해 Jedis에 비해 몇배 이상의 성능과 하드웨어 자원 절약이 가능합니다. 하지만 Spin Lock을 사용하여 반복적으로 락 획득을 시도하기 때문에 레디스에 많은 부하가 발생합니다. 또한 락에 대한 타임아웃이 없어, 락 해제 호출을 하지 못한 경우 Dead Lock을 유발할 수 있습니다.</br>

  **Redisson**는 pub/sub 방식을 사용하여 락을 당장 획득할 수 없으면 대기하고, 락이 획득 가능할 경우 Redis에서 클라이언트로 획득 가능함을 알려줍니다. 또한 락의 lease time 설정이 가능하기 때문에 설정된 lease time이 지난 경우 자동으로 Lock의 소유권을 회수하여 Dead Lock을 방지할 수 있습니다.</br>

  > 의사결정

  Pub/Sub 방식으로 동작해 Lettuce에 비해 Redis에 부하가 덜 가는 Redisson을 사용하기로 결정했습니다.
</details>
<details>
  <summary>4. ElastiCache</summary>
  
  > 도입 배경

  문자열, 해시, 리스트, 세트 등 다양한 데이터 구조를 지원하는 Redis를 사용하여 `RefreshToken, 캐싱 처리, 분산락 처리`를 해야 했습니다  

> 자체 관리 Redis VS ElastiCache

  자체 관리 Redis는 서버 설정, 모니터링, 업그레이드 등을 직접 관리해야 합니다. 이는 많은 시간과 자원을 소모합지만, ElastiCache는 이러한 작업을 자동으로 처리하여 운영 부담을 줄여줍니다. 그리고, 자체 관리 환경에서는 수요에 맞게 수동으로 서버를 추가하고 설정해야 하지만, ElastiCache는 자동 확장 기능을 제공하여, 필요에 따라 자동으로 리소스를 확장하거나 축소할 수 있습니다.

  > 의사결정

이에 따라 오토 스케일링이 가능하고, 운영 부담이 적은 ElastiCache를 선택하게 되었습니다.
</details>
<details>
  <summary>5. GitHub Action</summary>
  
  > 도입 배경

  반복적인 일들을 처리하고 자동화된 파이프라인을 통해서 코드 변경과 배포를 원활하게 하기 위해 CI/CD 를 구현해야 했습니다.

  > Jenkins VS Github Action

  `Jenkins`는 다양한 플러그인을 통해 거의 모든 CI/CD 시나리오를 지원하고, 복잡한 파이프라인 설정 및 사용자 정의가 가능하다는 장점이 있지만, 초기 설정과 유지 보수가 복잡하고 사용자가 직접 서버를 관리해야 한다는 단점이 있습니다.

`GitHub Action`은 GitHub와 깊이 통합되어 있어, GitHub 리포지토리 내에서 바로 CI/CD 파이프라인을 설정하고 관리할 수 있고, 서버 관리가 필요하지 않다는 장점이 있지만, 대규모 파이프라인을 구현하는 데는 제한이 있다는 단점이 있습니다.

  > 의사결정

  저희 팀은 아직 프로젝트가 크지 않기 때문에 대규모 파이프라인을 구현하는 것보다는, GitHub과의 직접적인 통합을 통해 워크플로우를 간소화 하는 것이 좋을 것이라고 판단하여 GitHub Action을 선택하였습니다.
</details>
<details>
  <summary>6. Docker, ECR, CodeDeploy</summary>
  
  > 선택 이유

**Docker**</br>
Docker를 사용하면 응용 프로그램을 서로 다른 환경에서도 일관되게 실행할 수 있고, 개발 환경과 운영 환경 사이의 차이로 인한 문제를 줄일 수 있다는 점에 Docker를 사용한 컨테이너 기반 배포 방식을 사용하기로 결정 하였습니다.

**AWS ECR**</br>
Docker Hub에도 프라이빗한 레포지토리가 있지만 프로젝트의 인프라를 AWS를 이용해서 구축하기 때문에 연계부분의 이점으로 도입하였습니다.
- Docker 이미지의 안전한 관리를 지원
- AWS 인프라와의 긴밀한 통합을 통해 우수한 보안성, 관리 용이성 및 확장성 제공
- IAM을 통한 세분화된 접근 권한 및 보안 관리 가능

**AWS CodeDeploy**</br>
CodeDeploy를 통해 배포 진행 상황 모니터링하고 배포 자동화를 위해 선택하게 되었습니다.
</details>
<details>
  <summary>7. Batch Scheduler</summary>
  
  > 도입 배경

  - 팝업 스토어의 **일별 매출, 그리고 통합 매출**을 매번 계산하는 것이 아닌, 따로 테이블을 만들어 관리하고 업데이트 해야 할 필요성을 느꼈습니다.
  - 팝업 스토어의 **진행 종료일**과, 오프라인 예약의 **진행일**이 지나면 상태를 자동으로 변경시켜 주어야 할 필요성을 느꼈습니다.

  > Batch Scheduler VS Quartz Scheduler

**Batch Scheduler**</br>
`대규모 데이터를 일괄 처리하는 데 최적화`되어 있습니다. 이는 대량의 데이터를 동시에 처리해야 하는 경우 특히 중요하며, 다른 스케줄러들에 비해 안정적이고 효율적인 성능을 제공합니다. 특정 환경에 맞게 커스터마이징이 용이하며, 기존의 시스템이나 다른 도구들과의 통합이 용이합니다. 이는 `기존 워크플로우에 Batch Scheduler를 무리 없이 도입`할 수 있게 해줍니다. </br>
하지만, 확장성 측면에서 한계가 있습니다. 특히 분산 처리나 클러스터링이 필요한 경우에는 추가적인 솔루션이 필요할 수 있으며,  `Spring에 의존`하기 때문에, Spring을 사용하지 않는 프로젝트에서는 사용이 어렵거나 불편할 수 있습니다.

**Quartz Scheduler**</br>
Quartz는 `자바 기반의 강력한 스케줄링 라이브러리`로, 복잡한 스케줄링 작업과 다양한 트리거 옵션을 통해 다양한 시나리오에 맞춤형 스케줄링을 구현할 수 있습니다. Persistent Job Store를 통해 작업 스케줄링을 영구 저장하고, 시스템 재시작 시에도 작업이 유지됩니다. </br>
하지만, `유연성이 높은 만큼 설정이 복잡`할 수 있으며, 특히 복잡한 스케줄링을 구성하려면 러닝 커브가 필요합니다. 또한, 다수의 작업을 메모리에 유지하기 때문에 메모리 사용량이 높아질 수 있으며, 대규모 작업을 처리할 때 시스템 리소스에 부담이 될 수 있습니다.

  > 의사결정

  현재 프로젝트가 Spring 기반의 프로젝트이며, 통계 데이터를 만들고 자동화 작업을 할 때에 서버 간 데이터 공유가 필요하지 않기 때문에 Quartz 까지는 사용할 필요가 없다고 생각하여 **Batch Scheduler**를 통해 통계 작업을 처리하도록 결정하였습니다.
</details>
</br>

## ✔️ 트러블 슈팅
<details>
  <summary>1. 동시성 테스트</summary>
  
  > 문제 발생

  분산락을 적용하기 전, 남은 개수가 100개인 쿠폰을 User 100명이 동시에 다운로드하는 테스트를 진행했습니다. 아직 동시성 제어가 안 되어있기 때문에 남은 개수가 0개도 100개도 아닌 값이 되어야 했지만, 이상하게도 100개가 그대로 남아있었습니다. 즉, 쿠폰이 하나도 안 줄어들었다는 것이며, 이는 모든 요청에 대한 트랜잭션이 전부 다 롤백되어서 update 쿼리가 하나도 안 나갔다는 뜻이었습니다.

```java
@SpringBootTest
public class CouponConcurrencyTest {

    @Autowired
    CouponService couponService;

    @Autowired
    CouponRepository couponRepository;

    @MockBean
    UserCouponRepository userCouponRepository;

    User user = User.createMockUser();

    Coupon coupon;

    int threadCount = 100;

    @BeforeEach
    void setUp() {
        coupon = Coupon.createMockCoupon(threadCount);
        couponRepository.save(coupon);
    }

    @Test
    void 쿠폰_다운로드_동시성_테스트_분산락_미적용() throws InterruptedException {
        // given
        given(userCouponRepository.existsByUserAndCoupon(any(User.class), any(Coupon.class)))
                .willReturn(false);
                
        // when
        ExecutorService executorService = Executors.newFixedThreadPool(threadCount);
        CountDownLatch latch = new CountDownLatch(threadCount);
        for (int i = 0; i < threadCount; i++) {
            executorService.submit(() -> {
                try {
                    couponService.downloadCouponWithoutLock(coupon.getId(), user);
                } finally {
                    latch.countDown();
                }
            });
        }
        latch.await();

        // then
        int remainingCount = couponRepository.findById(coupon.getId()).orElseThrow().getRemainingCount();
        assertThat(remainingCount).isNotZero().isNotEqualTo(threadCount);

        System.out.println("\n[remainingCount]");
        System.out.println("Expected = 0");
        System.out.println("Actual = " + remainingCount);
    }
}
```

  > 발생 원인

디버깅 해보니 `UserCouponResponseDto.of()`에 `null`이 들어가는 것을 확인했습니다. 이는 userCouponRepository가 MockBean인데, userCouponRepository.save의 결과를 따로 지정해주지 않았기 때문이었습니다. 따라서 UserCouponResponseDto에서 `null.getId()`를 하게 되고, 이에 따라 `NullPointerException`이 발생하여 트랜잭션이 롤백되기 때문에 원하던 결과가 나오지 않았던 것이었습니다. 

```java
@Transactional
public UserCouponResponseDto downloadCouponWithoutLock(Long couponId, User user) {
    Coupon coupon = couponRepository.findById(couponId)
            .orElseThrow(() -> new CustomException(COUPON_NOT_FOUND));
    // 이미 다운로드 받은 쿠폰인지 확인
    if (userCouponRepository.existsByUserAndCoupon(user, coupon)) {
        throw new CustomException(ALREADY_DOWNLOADED_COUPON);
    }
    coupon.download();
    UserCoupon userCoupon = userCouponRepository.save(UserCoupon.of(user, coupon));
    return UserCouponResponseDto.of(userCoupon);
}
```

```java
@Getter
public class UserCouponResponseDto {

    private final Long userCouponId;
    private final Long userId;
    private final Long couponId;

    private UserCouponResponseDto(UserCoupon userCoupon) {
        this.userCouponId = userCoupon.getId();
        this.userId = userCoupon.getUser().getId();
        this.couponId = userCoupon.getCoupon().getId();
    }

    public static UserCouponResponseDto of(UserCoupon userCoupon) {
        return new UserCouponResponseDto(userCoupon);
    }
    
}
```
이러한 NPE의 발생 여부를 몰랐던 이유는 스레드 병렬 수행에 쓰인 `CountDownLatch`가 다음 그림과 같이 예외를 먹기 때문이었습니다.


  > 문제 해결

  예외 발생 여부는 `CountDownLatch` 대신 `IntStream`을 사용함으로써 확인할 수 있었습니다. 둘 다 동일한 로직이지만 `CountDownLatch`는 테스트 도중 예외가 발생해도 그대로 수행하는 반면, `IntStream`은 예외가 발생하면 테스트를 중단하고 예외 정보를 출력한다는 차이점이 존재했습니다.

```java
@Test
void 쿠폰_다운로드_동시성_테스트_분산락_미적용() {
    // given
    given(userCouponRepository.existsByUserAndCoupon(any(User.class), any(Coupon.class)))
            .willReturn(false);

    // when
    IntStream.range(0, threadCount).parallel().forEach(i ->
            couponService.downloadCouponWithoutLock(coupon.getId(), user)
    );

    // then
    int remainingCount = couponRepository.findById(coupon.getId()).orElseThrow().getRemainingCount();
    assertThat(remainingCount).isNotZero().isNotEqualTo(threadCount);

    System.out.println("\n[remainingCount]");
    System.out.println("Expected = 0");
    System.out.println("Actual = " + remainingCount);
}
```

그리고 `UserCouponResponseDto.of()`에 `null`이 들어가지 않도록 다음과 같이 객체를 생성하는 로직과 repository에 save하는 로직을 분리해서 문제를 해결했습니다.

```java
@Transactional
public UserCouponResponseDto downloadCouponWithoutLock(Long couponId, User user) {
    Coupon coupon = couponRepository.findById(couponId)
            .orElseThrow(() -> new CustomException(COUPON_NOT_FOUND));
    // 이미 다운로드 받은 쿠폰인지 확인
    if (userCouponRepository.existsByUserAndCoupon(user, coupon)) {
        throw new CustomException(ALREADY_DOWNLOADED_COUPON);
    }
    coupon.download();
    
    UserCoupon userCoupon = UserCoupon.of(user, coupon);
    userCouponRepository.save(userCoupon);
    
    return UserCouponResponseDto.of(userCoupon);
}
```
</details>
<details>
  <summary>2. 분산락</summary>
  
  > 문제 발생

분산락을 적용한 후, 남은 개수가 100개인 쿠폰을 User 100명이 동시에 다운로드하는 테스트를 진행했습니다. 동시성 제어를 했기 때문에 남은 개수가 0개가 되어야 했지만, 이상하게도 조금씩 남아있었습니다. 즉, 분산락을 적용했지만 여전히 동시성 제어가 되지 않았습니다.

```java
@Transactional
public UserCouponResponseDto downloadCoupon(Long couponId, User user) {
    RLock lock = redissonClient.getFairLock("couponDownloadLock_" + couponId); // 요청 들어온 순서대로 처리
    boolean locked = false;
    try {
        locked = lock.tryLock(10, 60, TimeUnit.SECONDS);
        if (locked) {
				    Coupon coupon = couponRepository.findById(couponId)
				            .orElseThrow(() -> new CustomException(COUPON_NOT_FOUND));
				    // 이미 다운로드 받은 쿠폰인지 확인
				    if (userCouponRepository.existsByUserAndCoupon(user, coupon)) {
				        throw new CustomException(ALREADY_DOWNLOADED_COUPON);
				    }
				    coupon.download();
				    
				    UserCoupon userCoupon = UserCoupon.of(user, coupon);
				    userCouponRepository.save(userCoupon);
				    
				    return UserCouponResponseDto.of(userCoupon);
        }
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    } finally {
        if (locked && lock.isHeldByCurrentThread()) {
            lock.unlock(); // 락을 획득했을 때만 해제
        }
    }
    return null;
}@Transactional
public UserCouponResponseDto downloadCoupon(Long couponId, User user) {
    RLock lock = redissonClient.getFairLock("couponDownloadLock_" + couponId); // 요청 들어온 순서대로 처리
    boolean locked = false;
    try {
        locked = lock.tryLock(10, 60, TimeUnit.SECONDS);
        if (locked) {
				    Coupon coupon = couponRepository.findById(couponId)
				            .orElseThrow(() -> new CustomException(COUPON_NOT_FOUND));
				    // 이미 다운로드 받은 쿠폰인지 확인
				    if (userCouponRepository.existsByUserAndCoupon(user, coupon)) {
				        throw new CustomException(ALREADY_DOWNLOADED_COUPON);
				    }
				    coupon.download();
				    
				    UserCoupon userCoupon = UserCoupon.of(user, coupon);
				    userCouponRepository.save(userCoupon);
				    
				    return UserCouponResponseDto.of(userCoupon);
        }
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    } finally {
        if (locked && lock.isHeldByCurrentThread()) {
            lock.unlock(); // 락을 획득했을 때만 해제
        }
    }
    return null;
}
```

> 문제 원인

분산락을 적용한 코드를 보면 메서드에 `@Transactional` 애노테이션이 걸려있습니다. 해당 메서드가 호출되면 트랜잭션이 시작되고, 메서드 실행이 완료되면 트랜잭션이 커밋됩니다. 하지만 내부 코드를 보면 finally 문 안의 `lock.unlock()`을 실행하고 나서 메서드가 종료됩니다. 즉, 락을 해제하고 나서 트랜잭션이 커밋된다는 뜻입니다. 따라서 락이 해제되고 트랜잭션이 커밋되기 전, 이 짧은 찰나에 다른 스레드가 락을 획득한 것이었습니다.

> 문제 해결
해당 문제를 해결하려면 락을 해제하기 전에 트랜잭션을 커밋해야 했습니다. 이를 구현하기 위해 애노테이션 기반의 Spring AOP를 이용한 분산락을 적용했습니다.
```java
@Slf4j
@Aspect
@Component
@RequiredArgsConstructor
public class DistributedLockAspect {

    private static final String REDISSON_LOCK_PREFIX = "LOCK:";

    private final RedissonClient redissonClient;
    private final TransactionForAop transactionForAop;

    @Around("@annotation(com.sparta.ezpzuser.common.lock.DistributedLock)")
    public Object lock(final ProceedingJoinPoint joinPoint) throws Throwable {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        DistributedLock distributedLock = method.getAnnotation(DistributedLock.class);

        String key = REDISSON_LOCK_PREFIX +
                CustomSpELParser.getDynamicValue(
                        signature.getParameterNames(),
                        joinPoint.getArgs(),
                        distributedLock.key()
                );

        RLock rLock = redissonClient.getFairLock(key); // 선착순 보장

        try {
            log.info("try lock for key: {}", key);
            boolean available = rLock.tryLock(
                    distributedLock.waitTime(),
                    distributedLock.leaseTime(),
                    distributedLock.timeUnit()
            );
            if (!available) {
                throw new RuntimeException("lock failed for key: " + key);
            }
            // DistributedLock 어노테이션이 선언된 메서드를 별도의 트랜잭션으로 실행
            return transactionForAop.proceed(joinPoint);
        } catch (InterruptedException e) {
            throw new InterruptedException();
        } finally {
            // 반드시 트랜잭션 커밋 이후 락이 해제되도록 처리
            try {
                rLock.unlock();
            } catch (IllegalMonitorStateException e) {
                log.error("Redisson Lock is Already UnLocked");
            }
        }
    }
```
```java
@Component
public class TransactionForAop {

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public Object proceed(final ProceedingJoinPoint joinPoint) throws Throwable {
        return joinPoint.proceed();
    }

}
```
`@DistributedLock` 애노테이션이 선언된 메서드를 별도의 트랜잭션으로 실행함으로써 락을 해제하기 전에 트랜잭션 커밋을 하도록 구현했습니다. 하지만 다음과 같이 락을 얻는데 실패했습니다.


이는 CouponService 단에 `@Transactional(readonly = true)` 애노테이션을 걸어두었기 때문이었습니다. 분산락을 위한  `DistributedLockAspect`보다 트랜잭션 인터셉터가 먼저 등록되었기 때문에 상위에서 이미 트랜잭션이 시작되어 connection 문제가 발생한 것이었습니다. 따라서 트랜잭션 Propagation을 `REQUIRES_NEW`로 바꿔도 해결이 불가능했습니다. 해당 문제는 상위 `@Transactional`을 제거하거나 `DistributedLockAspect`에 `@Order(1) `애노테이션을 걸어서 빈 등록 순서를 트랜잭션 인터셉터보다 먼저 등록되게 수정함으로써 해결할 수 있었습니다.
</details>
<details>
  <summary>3. Redis Cache 처리</summary>
  
  > 문제 발생

Redis를 사용하여 캐시 처리를 하던 중, `Page`를 캐시할 때 **직렬화, 역직렬화**가 안 되는 오류가 발생

> 발생 원인

Redis를 활용한 캐시 처리를 할 때는, 캐시처리를 적용할 메소드의 반환 class에 **기본 생성자**가 있어야 하는데, **Page 클래스는 기본 생성자가 없기 때문에** 발생한 문제

> 해결 방법
`PageImpl<T>`을 상속 받은 `RestPage<T>` 라는 Wrapper class를 적용

```java
@JsonIgnoreProperties(ignoreUnknown = true, value = {"pageable"})
public class RestPage<T> extends PageImpl<T> {
	
	@JsonCreator(mode = JsonCreator.Mode.PROPERTIES)
	public RestPage(@JsonProperty("content") java.util.List<T> content,
					@JsonProperty("number") int page,
					@JsonProperty("size") int size,
					@JsonProperty("totalElements") long totalElements) {
		
		super(content, PageRequest.of(page, size), totalElements);
	}
	
	public RestPage(Page<T> page) {
		super(page.getContent(), page.getPageable(), page.getTotalElements());
	}
	
	public RestPage(List<T> content, Pageable pageable, Long total) {
		super(content, pageable, total);
	}
}
```
</br>

**기존 코드**
```java
public Page<ReservationResponseDto> findReservations(Pageable pageable, String status, User user) {
		ReservationStatus reservationStatus = ReservationStatus.valueOf(status.toUpperCase());
		Page<Reservation> reservationPage = reservationRepository.findByUserIdAndStatus(user.getId(), reservationStatus, pageable);
		validatePageableWithPage(pageable, reservationPage);
		
		return reservationPage.map(r -> ReservationResponseDto.of(r, r.getSlot()));
	}
```
</br>


**변경된 코드**
```java
	@Cacheable(value = "reservations", key = "#user.id + ':' + #status + ':' + #pageable.pageNumber")
	public RestPage<ReservationResponseDto> findReservations(Pageable pageable, String status, User user) {
		ReservationStatus reservationStatus = ReservationStatus.valueOf(status.toUpperCase());
		Page<Reservation> reservationPage = reservationRepository.findByUserIdAndStatus(user.getId(), reservationStatus, pageable);
		validatePageableWithPage(pageable, reservationPage);
		
		return new RestPage<>(reservationPage.map(r -> ReservationResponseDto.of(r, r.getSlot())));
	}
```
</details>
<details>
  <summary>4. DB Connection</summary>
  
> 문제 발생

RDS의 db.t3.micro 클래스를 이용하여 MySQL을 사용하던 중, 배포 과정에서 DB Connection이 50개를 초과하여 DB와 연결이 안 되는 문제 발생

> 발생 원인

DB의 Connection 관리를 제대로 해주지 않아서 발생한 문제

- 접속 가능한 max_connection 수 50으로 적었던 점
- timeout 을 설정하지 않아, 요청이 없는 connection을 계속 유지시켰던 점

> 문제 해결

- AWS에서 파라미터 그룹을 설정하여, 현재 사용하고 있는 MySQL RDS에 적용
    - connect_timeout : 180초
    - max_connections : 100개

- 데이터베이스에 3분 이상 요청이 없으면 `Connection을 해제`하는 방식을 사용
- 최대 연결할 수 있는 Connection을 `100개`로 증가
</details>
<details>
  <summary>5. ALB 리스너 규칙</summary>

> 문제 발생 

3개의 서버에 대한 요청을 한 개의 로드밸런서를 사용해서 처리하는 중, 
HTTP 요청에 따라 리스너 규칙을 적용하여서 User, Host, Admin 서비스에 맞는 서버로 요청을 라우팅 시키기로 하였다. </br>

프론트 쪽에서 HTTP의 `Host 헤더`를  `OOO.ezpzz.store` 의 형식으로 적용하여 요청을 라우팅하려고 시도하려고 하였으나 계속해서 실패하게 되었다.

> 발생 원인

- 브라우저가 Host 헤더를 클라이언트 코드에서 설정하려고 하는 시도를 차단하면서 발생하는 문제
- 보안상의 이유로 특정 **“unsafe” 헤더**(Host, Content-Length 등)를 클라이언트 측에서 직접 설정하거나 수정하는 것을 허용하지 않는 문제

> 문제 해결

방법을 찾던 중 각 사이트에서 `Referer 헤더` 값을 통해 라우팅을 시키는 방식을 고민하게 되었고,

로드밸런서에서 리스너 규칙을 `Referer 헤더` 값으로 변경하였고, 이를 통해 클라이언트 쪽에서 적절한 서버로 요청을 라우팅할 수 있게됨
</details>
<details>
    <summary>6. Docker 이미지 빌드</summary>

### 문제 1

> 문제 발생

Docker 이미지 빌드는 성공하지만, 실행이 안 되는 문제 발생

> **발생 원인**
> 

루트 폴더가 아닌, Docker 폴더 안에 DockerFile을 생성했기 때문에,
Docker 이미지 빌드 시, 도커 파일의 경로를 지정해주지 않아서 .jar 파일의 경로를 찾지 못해 실행이 되지 못한 것이었다

> 문제 해결

```
docker build . --file docker/Dockerfile --tag image-name:latest
```

이미지 빌드 DockerFile의 경로를 지정해두니 해결되었다

### 문제 2

> 문제 발생

```
WARNING: The requested image's platform (linux/arm64/v8) does not match the detected host platform (linux/amd64/v3) and no specific platform was requested

exec /usr/local/openjdk-17/bin/java: exec format error
```

로컬에서 도커 이미지 빌드는 잘되었지만 AWS EC2 인스턴스에서 docker run 명령어로 컨테이너를 실행할때 위와 같은 에러 발생했다

> 발생 원인

도커 이미지 빌드 시, 현재 빌드 플랫폼인 로컬 환경의 맥북 Apple M1과 EC2 서버의 호환성이 문제로 인한 것이었다.

> 문제 해결

```
docker build . --platform linux/amd64 -f docker/Dockerfile -t image-name:latest
```

로컬에서 빌드한 이미지가 **`linux/arm64/v8`** 이었고, 이를 **`linux/amd64`** 플랫폼 형태의 이미지로 새롭게 빌드하기 위해 위와 같은 명령어를 사용해 EC2 서버와 호환성을 맞춰주니 문제가 해결되었다.
</details>
<details>
  <summary>7. GitHub Actions gradle 빌드</summary>

> 문제 발생

GitHub Action runner에서 CD 자동화 파이프라인을 구축하던 중, .`/gradlew build` 명령어에서 에러가 발생했다

- Task :compileJava 단계에서 `Q클래스` 및 `lombok 애노테이션`을 사용한 메서드들을 못 읽는 에러가 발생했다
- Q클래스, lombok 을 못 읽는 걸로 보아 `annotationProcessor`의 문제라고 생각되어 관련 에러들을 모두 찾아보았지만 해결하지 못했다
- 그 이후로도 `gradle 설정`을 변경해보았지만 해결하지 못 했다
- 결국 프로젝트 및 gradle의 문제는 아니고 **`GitHub Action 환경의 문제`**라고 판단했다

> 발생 원인

문제의 발생 원인은 **GitHub Action, gralde의 캐시** 때문이었다

> 문제 해결

```
# 원본
- name: Build with Gradle Wrapper
  run: ./gradlew build

# 1. Github Cache 지우기
- name: Cache dependencies
  uses: actions/cache@v2
  with: 
    path: ~/.gradle/caches
    key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle*', '**/gradle-wrapper.properties') }}-v2
  
# 2. gradle 관련 캐시 전부 지우기  
- name: Build with Gradle Wrapper
  run: ./gradlew clean build --no-build-cache
- name: Clear Gradle Cache
  run: rm -rf ~/.gradle/caches/

# 캐시 지운 이후
- name: Build with Gradle
  run: ./gradlew clean build --stacktrace
```

- 첫번째 시도로 GitHub Action의 캐시를 삭제
- 두번째 시도로 gradle의 모든 캐시를 삭제

캐시를 삭제한 이후에는 gradle build가 원만하게 수행되었다
</details>
</br>

## ✨ Team EZPZ
|여운호|이희수|이민정|유동현|
|:-:|:-:|:-:|:-:|
|<img src="https://avatars.githubusercontent.com/u/122009132?v=4" height=200 width=200> | <img src="https://avatars.githubusercontent.com/u/114149212?v=4" height=200 width=200> | <img src="https://avatars.githubusercontent.com/u/60657536?v=4" height=200 width=200> | <img src="https://avatars.githubusercontent.com/u/64765991?v=4" height=200 width=200>|
|[@uh1205](https://github.com/uh1205)|[@llocr](https://github.com/llocr)|[@M1ngD0ng](https://github.com/M1ngD0ng)|[@ydh5017](https://github.com/ydh5017)|
