# Spring Cloud Netflix

## 1. Netflix OSS

### 1.1 등장 배경
  * 심각한 데이터베이스 손상으로 3일간의 DVD **배송이 지연**되는 문제가 발생하였습니다.

  * 수직으로 확장되는 **단일 실패 지점**(Netflix 데이터 센터의 **관계형 데이터베이스**)에서 벗어나기를 희망하였습니다.

  * 이후 Netflix는 **신뢰성 있고 수평 확장이 가능**한 클라우드 내 분산 시스템으로 이전해야 할 필요성을 체감하였습니다.

  * 최고의 확장성과 다양한 서비스를 제공하는 AWS를 클라우드를 선택하였습니다.

  * **자체 개발한 Netflix Stack**을 이용 2008년 8월 클라우드 마이그레이션을 시작하게 되었습니다.

### 1.2 '7'년의 마이그레이션

  * 클라우드의 장점이 많은데도 불구하고 7년이 걸려서 마이그레이션이 최종 완료 되었습니다.

  * 데이터 센터의 모든 시스템을 AWS로 가져다 놓는 것이 가장 쉬운 방법이지만 이러한 방법은 데이터 센터가 지닌 문제점과 한계점을 그대로 옮기는 것 밖에 되지 않습니다.

  * 클라우드 네이티브 방식을 택하여 사실상 모든 기술을 재구축하고 운영 방식을 바꾸게 되었습니다.

  * 거대한 어플리케이션을 수백 개의 마이크로 서비스로 마이그레이션 하고 NoSQL 데이터베이스를 사용하여 데이터 모델을 반정규화 하였습니다.

  * 중앙화된 릴리즈 관리, 몇 주에 걸친 하드웨어 프로비저닝 주기를 도입해 지속적인 콘텐츠 전달이 가능하도록 하였습니다.

  * 느슨하게 개발 된 개발운영(DevOps) 환경에서 엔지니어링이 셀프서비스 툴로 독립적인 결정을 내릴수 있게 하였습니다.

  * 많은 리소스를 차지하는 새로운 기능이 도입되고 데이터 사용량이 증가함에도 클라우드 탄력성 덕분에 수천 개의 가상 서버와 페타바이트급 저장 용량을 단 몇 분 내에 추가 할 수 있게 되었습니다.(기존 넷플릭스 자체 데이터 센터에서는 탄력성이 떨어짐)

  * 데이터 센터의 경우 서비스 중단이 잦았지만 지금은 서비스 가용성이 증가하여 **서비스 가동시간 99.99% 목표**에 다가 서게 되었습니다.


### 1.3 Netflix OSS 공개 이유
  * 오픈 소스에 기여한 많은 사람들로부터 이익을 얻었기 때문에 기여하고 싶었습니다.

  * 모든 스트리밍 서비스를 AWS 인프라 위에서 실행할 수 있는 초기 클라우드 어댑터였고 그로 인해서 많은 문제들과 한계에 직면하게 되었습니다.

  * 여기서 플래폼 구성 요소 및 자동화 툴에 적용되는 패턴을 파악하였고 **유사한 패턴을 채택하는 서비스 생태계 발전**을 위해 공개 하였습니다.

  * 대규모의 개발자 커뮤니티를 만들어 본인들의 컴포넌트에 대한  버그 수정, 리뷰, 테스트 케이스 추가와 같은 기여를 통해 모두에게 혜택을 제공 할 분만 아니라 여러 피드백을 통해서 해당 기능들을 향상 시킬 수 있습니다.

## 2. Eureka
### 2.1 Eureka란?
* Eureka는 AWS 클라우드에서 middle-tier 서버의 로드 밸런싱 및 페일 오버를 위해 서비스를 찾는데 사용되는 REST 기반 서비스입니다.

* Eureka Server와 Java 기반 클라이언트 구성 요소인 Eureka Client와 함께 제공합니다.

* 클라이언트에는 기본 라우드 로빈 로드 밸런싱을 수행하는 로드 밸런서가 내장되어 있습니다.

* Eureka를 감싸고 있는 정교한 로드 밸런서는 트래픽, 리소스 사용, 오류 조건 등과 같은 여러 요소를 기반으로 가중된 로드 밸런싱을 제하여 우수한 탄력성을 제공합니다.

### 2.2 Eureka의 필요성
* 클라우드는 본질적인 특성 때문에 네트워크 위치(IP 주소 및 포트)가 동적으로 할당 되고 변경이 일어 날 수 있습니다. 즉 아래 그림과 같이 클라이언트가 호출 할 서비스 인스턴스 주소를 확실히 알 수가 없다는 것이 문제입니다.

![](https://cdn-1.wp.nginx.com/wp-content/uploads/2016/04/Richardson-microservices-part4-1_difficult-service-discovery.png)

* 클라이언트는 사용 가능한 서비스 인스턴스의 네트워크 위치를 결정하고 클라이언트 로드 밸런싱을 담당하기 위해서는 아래 그림과 같이 서비스 인스턴스의 정보를 가진 레지스트리가 필요하게 되었습니다.

![](https://cdn-1.wp.nginx.com/wp-content/uploads/2016/04/Richardson-microservices-part4-2_client-side-pattern.png)

* AWS 로드 밸런싱은 로드밸런서를 사용하여 서버 등록 및 등록 취소 작업을 훨씬 더 정교하게 수행해야 합니다.(IP와 Host Name 기반으로 작동하기 때문)

* AWS ELB는 server-side-discovery-router이며 별도의 레지스트리가 없고 EC2인스턴스는 ELB자체에 등록 됩니다. 따라서 middle-tier 로드 밸런서를 위해서는 eureka가 필요합니다.

### 2.3 AWS ELB와의 차이
* AWS ELB는 최종 사용자 웹 트래픽에 노출되는 Edge서비스를 위한 로드 밸런싱, Eureka는 중간 계층 로드 밸런싱의 필요성을 충족 시켜줌

* 중간 계층 서비스를 AWS ELB 뒤에 둘 수 있지만, 이것을 외부에 노출 시키면 AWS Security Group의 기능이 손실 됨

* AWS ELB는 전통적인 프록시 기반 로드 밸런싱이지만 Eureka의 로드 밸런싱은 instance, server, host 수준에서 일어남
(클라이언트 인스턴스는 접근이 필요한 서버에 대한 정보를 알고 있고 직접 연결이 가능함)

* Eureka 로드밸런싱과 프록시 기반의 로드 밸런싱의 큰 차이점 중 하나는 서버에 관한 정보가 클라이언트에 캐시되므로 응용 프로그램이 로드 밸런스의 장애에 대해 복원력을 가질 수 있음 (메모리를 차지하지만 탄력성이 좋음)

### 2.4 Eureka서버와 Eureka 클라이언트의 통신
* 아래 그림과 같이 Eureka client를 내장한 Application Service와 Client가 Eureka Server에 등록을 요청하고 30초마다 갱신을 위한 하트 비트를 보냅니다.

![](https://cdn-1.wp.nginx.com/wp-content/uploads/2016/04/Richardson-microservices-part4-4_self-registration-pattern.png)

* 이러한 등록 정보와 갱신 정보는 클러스터의 모든 유레카 노드에 복제가 되고 클라이언트들은 레지스트리 정보를 찾아 해당 서비스를 찾고 원격 호출이 가능하게 됩니다.

![Eureka](https://github.com/Netflix/eureka/raw/master/images/eureka_architecture.png)

## 3. Zuul

### 3.1 Zuul 의 필요성

* Netflix의 스트리밍 응용 프로그램은 서로 연결 되어서 복잡한 시스템을 구성하고 있었고 클라우드 내 분산 시스템으로 전환함에 있어서 다양한 장치 유형을 지원하고 다양한 요청을 받기 위한 진입점이 필요하게 되었습니다.

* AWS region은 국제적인 확장을 지원하기 위해 새로운 국가에 배치되고 카탈로그가 추가 되는데 이러한 변화를 처리하기 위해서는 신속한 개발, 뛰어난 유연성, 복원력을 가능하게 하는 엣지 서비스가 필요

* 2013년 기준 Netflix API는 1000개가 넘는 디바이스 유형을 지원하고 피크 시간에 초당 50000개 이상의 요청을 넘기는 Zuul(Edge Service)을 구축하고 있음

### 3.2 Zuul Filter 동작 방식

![Zuul](https://cdn-images-1.medium.com/max/1600/1*9IeEGHSRMGfAnhqM49TLpQ.png)

* Pre 필터는 라우팅 직전에 인증, Origin Server 선택, 디버그, 정보 로깅등을 담당

* Routing 필터는 요청은 Origin Server로 라우팅하는 것을 처리하고 이 단계에서 리본을 사용

* Post 필터는 요청이 라우팅 된 이후에 실행

* Error 필터는 다른 단계 중 하나에서 오류가 발생하면 실행

### 3.3 Zuul의 기능
API 트래픽의 양과 다양성으로 인해 경고 없이 문제가 발생할 수 가 있습니다.

Zuul은 다양한 유형의 필터를 사용하여 신속하게 edge service에 기능을 적용 할 수 있습니다.

여러 필터를 이용해 아래와 같은 기능을 수핼 할 수 있습니다.

* 인증 및 보안 - 각 리소스에 대한 인증 요구사항을 식별하고 이를 만족시키지 않는 요청을 거부합니다.

* 통찰력 및 모니터링 - 다른 Netflix OSS 구성요소를 함께 사용하여 의미 있는 데이터 및 통계를 추적하여 정확한 보기를 제공합니다.
  * Hystrix - 문제가 발생 할 때 트래픽 차단

  * Ribbon - Zuul의 아웃 바운드 요청에 대한 클라이언트로서 네트워크 성능 및 오류에 대한 자세한 정보를 제공하고 부하 분산을 위해 소프트웨어적인 부하를 분산시킵니다.

  * Turbine - 실시간으로 세분화 된 메트릭을 집계합니다.

  * Archaius - 구성을 처리하고 속성을 동적으로 변경합니다.

  * 동적 필터링 - 동적 필터링 기능을 통해 대량의 요청 중에서 일반적으로 찾기 어려운 문제를 찾아서 격리를 시킬 수 있습니다. 특정 고객이나 장치를 디버깅 하기 위해 별도의 API클러스터로 라우팅하도록 필터를 작성 할 수 있습니다. 예전에는 Hadoop을 사용하여 관심을 갖고 있던 수천 건의 요청건을 찾기 위해 수십억개의 기록을 쿼리로 찾았습니다.

* Stress Test - 필터 내에서 동적 Archaius 구성을 사용하여 소규모 서버로 라우팅 되는 트래픽을 지속적으로 증가시키는 자동화 된 프로세스를 보유하고 있습니다. 인스턴스가 더 많은 트래픽을 받게 되면 성능과 용량을 측정합니다. 이렇게 하면 피크 타이밍에 자동 스케일링 정책이 수정이 필요한지 아닌지와 우리가 필요한 EC2의 인스턴스의 갯수를 알려줍니다.

* Multi-Region 복원 - Zuul은 Isthmus(지협)이라 불리는 Multi-Region ELB 복원 프로젝트의 핵심입니다.
  * Isthmus - ELB 가동 중단과 같은 문제를 견딜 수 있도록 아키텍처를 강화하였고 이러한 중단을 극복하기 위해 다른 지역에 호스팅 된 ELB 세트를 사용하도록 하는 방법 (ELB 문제에 대한 탄력성)
  
  ![](https://cdn-images-1.medium.com/max/1600/0*-NotvSIpVgdrweCP.)

  ![](https://cdn-images-1.medium.com/max/1600/0*q0BO8z2eG9JLPRdf.)


## 4. Ribbon

Netflix는 클라우드 기반에서 정교한 서비스를 지향하는 아키텍처를 지향합니다. 현재 Netflix API 서비스와 같은 수백개의 정교한 서비스를 Edge 서비스를 통해서 지원하고 있습니다. 경량급 REST 기반 프로토콜은 이러한 서비스 프로세스 간 통신을 위한 선택입니다.

Ribbon은 소프트웨어 로드 밸런싱 알고리즘과 함께 Netflix Internal Web Service Framework (NIWS) 를 구성하는 몇가지 요소를 제공합니다. **유레카와 함께 사용되는 내부 클라이언트 로드 밸러서**로서 주로 **middle-tier 서비스에 대한 로드 밸런싱** 요청에 사용되고 있습니다.

### 4.1 Ribbon의 역할
* Netflix의 대표적인 배포 아키텍쳐
![ribbon](https://cdn-images-1.medium.com/max/1600/1*rTuC4j1ouqJUbHMApiUCzg.png)

* Netflix의 일반적인 배포 아키텍처는 가용성과 탄력성을 향상시키는 **다중 지역, 다중 영역 배포**입니다.

* **Ribbon 프레임워크는 middle-tier간 요청**을 로드 밸런싱 처리하고 반면 **Amazon ELB는 고객과 장치 요청에 대한 로드 밸런싱을 수행**합니다.

* Ribbon 클라이언트는 대상 서비스 각각에 대해 생성 되고 구성이 됩니다. Ribbon 클라이언트 구성 요소는 **연결 시간 제한, 재시도, 재시도 알고리즘 등을 제공**합니다.

* Ribbon 내부에는 사용자가 정의 가능한 로드 밸런싱 구성 요소가 내장 되어 있습니다. 제공되는 **로드밸런싱 전략** 중 일부는 다음과 같습니다.
  * Simple Round Robin LB

  * Weighted Response Time LB

  * Zone Aware Round Robin LB

  * Random LB

### 4.2 Battle Tested Features
* Ribbon의 주요 이점은 Amazon Cloud에서 운영하면서 얻은 경험을 기반으로 구축된 상호 작용 통신 메커니즘을 제공합니다.

* Ribbon의 **Zone Aware Round Robin 로드 밸런서**는 회로 트릭 로직과 함께 구축되어 지역 선호도(**Zone Affinity**)에 따라 특정한 대상 서비스 인스턴스를 선호하도록 구성 될 수 있습니다.

* 이러한 방식은 특정한 Zone이 갑작스럽게 운영 중단 되어도 탄력적으로 대응이 가능합니다.


### 4.3 Zone Aware Load Balancer (지역 선호 로드 밸런서)
![](https://cdn-images-1.medium.com/max/1600/0*SkYmTGU2e15XElZA.gif)
* 로드 밸런서는 사용 가능한 모든 구역의 통계를 계산하고 검사합니다.

* 서버 별 활성 요청이 설정한 임계 값에 도달하면 Zone은 활성 서버 목록에서 삭제 됩니다.

**(Average Active Requests = outstanding request / (instance수 - tripping instance))**

* 최악의 Zone이 삭제 되면 나머지 Zone 중에서 인스턴스의 수에 비례해서 Zone이 선택 됩니다.

* 선택된 Zone에서 요청을 받을 서버는 Ribbon에 설정한 로드밸런싱 전략에 따라 반환 됩니다.

## 5. Hystrix

### 5.1 결함에 대한 내성(Fault Tolerance)
* 결함에 대한 내성은 필요 사항입니다. 간헐적인 실패는 서비스가 아무리 뛰어난 가용성과 가동시간을 가지고 있더라도 매우 큰 변수가 될 수 있습니다.

* 하나의 API 의존성이 대기 시간 증가와 함께 실패하게 된다면 사용 가능한 모든 요청 스레드를 포화시키고 API를 다운 시킬 수 있습니다.

* 따라서 아키텍처에 결함에 대한 내성을 구축하고 인프라가 이를 해결 할 것이라는 기대감을 갖지 않는 것이 고가용성 어플리케이션의 요구 사항입니다.

### 5.2 Hystrix 란?
* 서비스 간 액세스 지점을 격리하고 이를 통해 계단식 오류를 중지하며 대체 옵션(fall back)을 제공하여 전반적인 복원력을 향상 시키는 솔루션입니다.

* third-party 라이브러리를 통해 접근 된 종속성으로부터 보호하고 장애를 제어합니다.

* 실패를 빠르고 반복적으로 복원합니다.

* 실시간으로 모니터링을 가능하게 합니다.

### 5.3 회로 차단기 (Circuit breaker)
* 마이크로서비스 혹은 분산 아키텍처 상에는 수십가지의 종속성이 있고 원치 않게 어느 시점에서 실패할 가능성이 존재합니다. 이러한 오류로부터 격리되지 않으면 다른 응용프로그램 마저 종료 될 수 있습니다.

* 99.99%의 가동성을 가진 30개의 서비스가 있을 경우 각각에 대해 0.01%의 중단 시간이 미치는 전체적인 영향은 전체 시스템에서 한달에 한시간 이상의 중단 시간을 초래 할 수 있습니다.

* 아래의 그림처럼 대용량 트래픽이 발생하면 백엔으 종속성이 잠재 되어 있는 모든 서버에서 모든 리소스가 몇 초내에 포화 상태가 될 수 있습니다. 실패보다 더 심각 한 것은 이러한 응용 프로그램이 대기열, 스레드 및 서비스 간의 대기 시간을 증가 시켜 계단식 오류를 발생 시킬 수 있습니다.

![fail](https://github.com/Netflix/Hystrix/wiki/images/soa-3-640.png)

* 이러한 문제는 thrid party 라이브러리를 사용 할 경우(특히 내부를 알 수 없고 언제든지 변경 할 수 있는 black box로 제공된 경우) 더욱더 악화가 될 수 있습니다.

* 따라서 아래 그림과 같이 **실패가 일어날만한 지점(종속성이 있는 부분)에 회로 차단기를 설치**합니다.

![circuitbreaker1](https://cdn-images-1.medium.com/max/2000/1*YyU4z_oOw3clyhYfwLdraQ.png)

* 회로 차단기는 쓰레드 pool, timeout, 로직적인 실패 등을 탐지하여 만약 실패가 발생한다면 빠르게 fallback을 수행합니다.

![circuitbreaker2](https://cdn-images-1.medium.com/max/2000/1*9RJt_f36SjK4PM1bH6Dkmg.png)


* 의존성 호출을 별도의 스레드로 분리하면 얻을 수 있는 장점이 단점보다 크다고 판단이 되었고 최종적인 그림은 아래와 같이 별도의 스레드 풀을 통해 라우팅 되게 됩니다. 하위 시스템에 대한 최악의 고장 유형 중 하나인 의존성이 숨어버린 경우, 자체 스레드 풀의 모든 스레드를 포화 시킬 수 있지만 Tomcat 요청 스레드는 블로킹 되지 않고 단순히 시간이 초과되거나 거부 됩니다.

![final](https://cdn-images-1.medium.com/max/1600/1*wgCMfYG_Do4-0mZLh01JWA.png)

* 별도의 스레드 오버헤드가 크기 때문에 메모리 내 캐시 검색만 수행과 같은 네트워크 호출을 수행하지 않을 경우 세마포어가 사용 됩니다. 또한 대체기능(fallback)에도 세마포어가 사용 됩니다.


## 6. Spring Cloud Netflix
* Spring은 Netflix OSS를 Spring Boot 프로젝트와 쉽게 통합 할 수 있도록 Spring Cloud Netflix를 만들게 됨

* Spring Cloud Netflix는 Spring Boot어플리케이션을 위한 Netflix OSS통합을 제공

* 간단한 어노테이션을 사용하면 Netflix OSS에서 사용되는 모든 패턴을 신속하게 활성화 및 구성하여 확장 가능한 성능 분산 시스템을 구축 할 수 있게 됨

* 제공 되는 서비스로는 Eureka, Hystrix, Zuul, Ribbon, Archaius, Sidecar 등이 포함 됨




## 7. 출처
* eureka
  * https://medium.com/netflix-techblog/netflix-shares-cloud-load-balancing-and-failover-tool-eureka-c10647ef95e5
  * https://www.nginx.com/blog/service-discovery-in-a-microservices-architecture/

* zuul
  * https://medium.com/netflix-techblog/announcing-zuul-edge-service-in-the-cloud-ab3af5be08ee

* ribbon
  * https://medium.com/netflix-techblog/announcing-ribbon-tying-the-netflix-mid-tier-services-together-a89346910a62

* hystrix
  * https://medium.com/netflix-techblog/fault-tolerance-in-a-high-volume-distributed-system-91ab4faae74a
  * https://medium.com/netflix-techblog/introducing-hystrix-for-resilience-engineering-13531c1ab362

* 기타
  * https://medium.com/netflix-techblog/a-closer-look-at-the-christmas-eve-outage-d7b409a529ee
  * https://media.netflix.com/ko/company-blog/completing-the-netflix-cloud-migration
