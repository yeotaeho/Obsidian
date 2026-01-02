 프로젝트- 모듈 - 서브모듈


OOP가 “데이터와 행위를 객체로 묶는다”면,
AOP는 “객체들의 공통된 행동 패턴을 따로 떼어낸다” 


횡단 관심

종단 
책임연쇄패턴

컨->서->레->엔 이것을 종단

컨->서->레->엔
컨->서->레->엔
컨->서->레->엔
세로 열이 횡단

![[Pasted image 20251112121636.png]]

 주요 개념

- **횡단 관심사(Cross-cutting Concerns):** 여러 모듈이나 계층에서 공통적으로 필요한 기능들을 의미합니다.
- **애스펙트(Aspect):** 횡단 관심사를 모듈화한 단위로, 부가 기능과 적용 시점을 정의합니다.
- **어드바이저(Advisor):** 어드바이스(부가 기능)와 포인트컷(적용 시점)을 합친 개념입니다.
- **프록시(Proxy):** 스프링은 기본적으로 프록시 패턴을 사용하여 AOP를 구현합니다. 대상 객체를 감싸는 프록시 객체를 생성하여, 프록시가 부가 기능을 먼저 실행하고 핵심 로직을 호출하도록 합니다.

주요 장점

- **코드 중복 제거:** 여러 곳에서 반복되는 부가 기능을 한곳에서 관리합니다.
- **유지보수성 향상:** 부가 기능의 변경이 필요할 때 해당 애스펙트만 수정하면 되므로 유지보수가 용이합니다.
- **비즈니스 로직 집중:** 핵심 비즈니스 로직과 부가 기능을 분리하여, 개발자가 비즈니스 로직 구현에 더 집중할 수 있게 합니다.
- **OOP 보완:** 객체 지향 프로그래밍만으로는 해결하기 어려운 횡단 관심사 문제를 해결합니다.

OOP가 “데이터와 행위를 객체로 묶는다”면, AOP는 “객체들의 공통된 행동 패턴을 따로 떼어낸다”입니다. 
개념 구조 Join Point 실행 가능한 지점 (메서드 호출, 예외 발생 등) → 예: public void save(User user) 호출 순간 Pointcut Join Point 중 실제 관심 있는 지점만 필터링 → 예: @annotation(RequestMapping) 붙은 메서드만 Advice 실제 수행할 코드 (관심사 로직) → 예: “로깅”, “시간 측정”, “트랜잭션 시작/종료” Aspect Pointcut + Advice 의 조합 Weaving Aspect를 실제 코드 실행 시점에 주입하는 과정 → Spring에서는 프록시(Proxy) 기반으로 Weaving 수행 3. OOP vs AOP 시각 비교 구분 OOP AOP 중심 단위 클래스, 객체 관심사(Concern) 설계 방향 데이터 중심 기능(관점) 중심 코드 중복 공통 로직이 여러 클래스에 흩어짐 공통 로직을 한 곳에 집중 예시 UserService, OrderService LoggingAspect, SecurityAspect 4. Spring AOP 기본 코드 예시


@Joinpoint


aop에 기능을 활용하기 위해 구조을 봐꾼다
일반문을 자동문으로 봐꾸기 위한 구조을 


![[Pasted image 20251112121807.png]]

서비스 붙인 이유 aop에서 pointcut이 찾기 용이하게?


@crosscuttting

[@Aspect](https://x.com/Aspect)

[@Component](https://x.com/Component)

public class LoggingAspect { // Pointcut: controller 패키지의 모든 메서드 실행 시

[@Pointcut](https://x.com/Pointcut)

("execution(* com.example.controller..*(..))") public void controllerMethods() {} // Advice: 실행 전(before)

[@Before](https://x.com/Before)

("controllerMethods()") public void logBefore(JoinPoint joinPoint) { System.out.println("[LOG] 호출된 메서드: " + joinPoint.getSignature().getName()); } // Advice: 실행 후(after) @AfterReturning(pointcut = "controllerMethods()", returning = "result") public void logAfter(JoinPoint joinPoint, Object result) { System.out.println("[LOG] 반환값: " + result); } }

[@Aspect](https://x.com/Aspect)

: 이 클래스가 AOP의 관점 역할을 한다는 선언 execution(* com.example.controller..*(..)): controller 하위 모든 메서드를 포인트컷으로 지정

[@Before](https://x.com/Before)

, @AfterReturning: 메서드 실행 전/후의 행동 정의 5. MSA에서 AOP 활용 포인트 영역 적용 예시 Gateway 요청 로깅, 트레이싱, JWT 인증 필터 Service Layer 트랜잭션 관리, 예외 로깅, 성능 측정 Repository Layer 쿼리 실행 시간 측정, 에러 처리 Event Bus (Kafka, RabbitMQ) 메시지 송수신 로깅, 장애 알림 Feign Client / RestTemplate 외부 호출 로깅 및 CircuitBreaker 결합 특히 MSA에서는 로깅 일관성(Log Correlation) 을 위해 AOP로 traceId를 MDC(Mapped Diagnostic Context)에 주입해주는 것이 핵심입니다. → 이렇게 하면 로그집계 서버(ELK, Grafana Loki 등)에서 서비스 간 호출을 한 트랜잭션 단위로 추적할 수 있습니다. 6. 단계별 적용 전략 1단계: 단일 모듈 공통 로깅 Controller, Service 단의 요청/응답 로깅 2단계: 예외 처리 Aspect 추가 공통 Exception 핸들러에서 ErrorAspect로 통합 3단계: 성능/트레이싱 Aspect

[@Around](https://x.com/Around)

사용해 메서드 실행 시간 측정 4단계: JWT, SecurityAspect 공통 인증 절차를 AOP로 위임 5단계: 분산 추적 (MSA 통합 로그) Sleuth, Zipkin, OpenTelemetry 등과 결합 7. 실제 MSA 전환에서의 역할 Spring Cloud, FastAPI, NestJS 등 MSA 프레임워크를 써도 AOP의 사상은 “미들웨어화(Intercept)” 개념으로 그대로 유지됩니다. 즉, AOP는 스프링의 철학을 미시적 단위의 필터 체인으로 확장시키는 기술입니다. OOP에서 설계된 도메인 로직은 그대로 두되, AOP로 서비스 간 공통 기능(로그·보안·트랜잭션) 을 “관점”으로 분리해 관리하면 유지보수성, 추적성, 통합성이 크게 향상됩니다. 원하신다면 다음 단계로,

[@Around](https://x.com/Around)

를 사용한 실행 시간 측정 Aspect 예제 TraceId를 각 서비스 로그에 자동 주입하는 Aspect 예제 도 단계별로 직접 구현 코드로 보여드릴 수 있습니다. 다음으로 어떤 예시부터 보고 싶으신가요?


![[Pasted image 20251113101036.png]]
인스턴스? 등록함

모듈화?

라우팅->마이크로서비스->db



