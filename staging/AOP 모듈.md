# AOP — 횡단 관심사를 모듈로 떼어내기

프로젝트 구조는 보통 **프로젝트 → 모듈 → 서브모듈**로 내려갑니다. 이 구조 위에서 공통 기능을 어떻게 다룰지가 AOP의 출발점이었습니다.

> **OOP가 "데이터와 행위를 객체로 묶는다"면, AOP는 "객체들의 공통된 행동 패턴을 따로 떼어낸다"입니다.**

## 1. 종단 관심사와 횡단 관심사

요청은 `컨트롤러 → 서비스 → 레포지토리 → 엔티티` 순서로 흐릅니다. 이 흐름 하나하나를 **종단(세로 흐름)** 이라고 보면, 여러 종단에서 공통으로 반복되는 가로 방향의 관심사가 **횡단(Cross-cutting)** 입니다.

```
컨 → 서 → 레 → 엔
컨 → 서 → 레 → 엔   ← 각 줄이 "종단"
컨 → 서 → 레 → 엔
─────────────────  ← 세로로 가로지르는 공통 기능이 "횡단"
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251112121636.png]]

## 2. 주요 개념

- **횡단 관심사(Cross-cutting Concerns):** 여러 모듈이나 계층에서 공통적으로 필요한 기능들을 의미합니다.
- **애스펙트(Aspect):** 횡단 관심사를 모듈화한 단위로, 부가 기능과 적용 시점을 정의합니다.
- **어드바이저(Advisor):** 어드바이스(부가 기능)와 포인트컷(적용 시점)을 합친 개념입니다.
- **프록시(Proxy):** 스프링은 기본적으로 프록시 패턴을 사용해 AOP를 구현합니다. 대상 객체를 감싸는 프록시 객체를 생성하여, 프록시가 부가 기능을 먼저 실행한 뒤 핵심 로직을 호출하도록 합니다.

## 3. 핵심 용어 구조

| 용어 | 의미 | 예시 |
| --- | --- | --- |
| **Join Point** | 실행 가능한 지점(메서드 호출, 예외 발생 등) | `public void save(User user)` 호출 순간 |
| **Pointcut** | Join Point 중 실제 관심 있는 지점만 필터링 | `@annotation(RequestMapping)`이 붙은 메서드만 |
| **Advice** | 실제 수행할 코드(관심사 로직) | 로깅, 시간 측정, 트랜잭션 시작/종료 |
| **Aspect** | Pointcut + Advice의 조합 | `LoggingAspect` |
| **Weaving** | Aspect를 실제 코드 실행 시점에 주입하는 과정 | 스프링에서는 프록시(Proxy) 기반으로 수행 |

## 4. OOP vs AOP

| 구분 | OOP | AOP |
| --- | --- | --- |
| **중심 단위** | 클래스, 객체 | 관심사(Concern) |
| **설계 방향** | 데이터 중심 | 기능(관점) 중심 |
| **코드 중복** | 공통 로직이 여러 클래스에 흩어짐 | 공통 로직을 한 곳에 집중 |
| **예시** | `UserService`, `OrderService` | `LoggingAspect`, `SecurityAspect` |

## 5. 주요 장점

- **코드 중복 제거:** 여러 곳에서 반복되는 부가 기능을 한곳에서 관리합니다.
- **유지보수성 향상:** 부가 기능이 바뀔 때 해당 애스펙트만 수정하면 됩니다.
- **비즈니스 로직 집중:** 핵심 로직과 부가 기능을 분리해, 개발자가 비즈니스 로직에 집중할 수 있습니다.
- **OOP 보완:** 객체 지향만으로는 풀기 어려운 횡단 관심사 문제를 해결합니다.

## 6. Spring AOP 기본 코드 예시

기존의 평범한 흐름을, AOP를 활용할 수 있는 구조로 바꾼다고 생각하면 됩니다. **"일반문을 자동문으로 바꾸는" 구조 변경**에 가깝습니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251112121807.png]]

```java
@Aspect
@Component
public class LoggingAspect {

    // Pointcut: controller 패키지의 모든 메서드 실행 시
    @Pointcut("execution(* com.example.controller..*(..))")
    public void controllerMethods() {}

    // Advice: 실행 전(before)
    @Before("controllerMethods()")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("[LOG] 호출된 메서드: " + joinPoint.getSignature().getName());
    }

    // Advice: 실행 후(after)
    @AfterReturning(pointcut = "controllerMethods()", returning = "result")
    public void logAfter(JoinPoint joinPoint, Object result) {
        System.out.println("[LOG] 반환값: " + result);
    }
}
```

- `@Aspect`: 이 클래스가 AOP의 관점 역할을 한다는 선언입니다.
- `execution(* com.example.controller..*(..))`: controller 하위 모든 메서드를 포인트컷으로 지정합니다.
- `@Before`, `@AfterReturning`: 메서드 실행 전/후의 행동을 정의합니다.

## 7. MSA에서 AOP 활용 포인트

| 영역 | 적용 예시 |
| --- | --- |
| Gateway | 요청 로깅, 트레이싱, JWT 인증 필터 |
| Service Layer | 트랜잭션 관리, 예외 로깅, 성능 측정 |
| Repository Layer | 쿼리 실행 시간 측정, 에러 처리 |
| Event Bus (Kafka, RabbitMQ) | 메시지 송수신 로깅, 장애 알림 |
| Feign Client / RestTemplate | 외부 호출 로깅 및 CircuitBreaker 결합 |

특히 MSA에서는 **로그 일관성(Log Correlation)** 을 위해, AOP로 `traceId`를 MDC(Mapped Diagnostic Context)에 주입하는 것이 핵심입니다. 이렇게 하면 로그 집계 서버(ELK, Grafana Loki 등)에서 서비스 간 호출을 하나의 트랜잭션 단위로 추적할 수 있습니다.

## 8. 단계별 적용 전략

1. **단일 모듈 공통 로깅** — Controller, Service 단의 요청/응답 로깅
2. **예외 처리 Aspect 추가** — 공통 Exception 핸들러를 `ErrorAspect`로 통합
3. **성능/트레이싱 Aspect** — `@Around`로 메서드 실행 시간 측정
4. **JWT, SecurityAspect** — 공통 인증 절차를 AOP로 위임
5. **분산 추적(MSA 통합 로그)** — Sleuth, Zipkin, OpenTelemetry 등과 결합

## 9. MSA 전환에서의 역할

Spring Cloud, FastAPI, NestJS 같은 MSA 프레임워크를 써도 AOP의 사상은 **"미들웨어화(Intercept)"** 개념으로 그대로 유지됩니다. 즉 AOP는 스프링의 철학을 **미시적 단위의 필터 체인**으로 확장하는 기술입니다.

OOP에서 설계한 도메인 로직은 그대로 두고, AOP로 서비스 간 공통 기능(로그·보안·트랜잭션)을 "관점"으로 분리해 관리하면 유지보수성·추적성·통합성이 크게 향상됩니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251113101036.png]]

전체 흐름은 결국 `라우팅 → 마이크로서비스 → DB`로 이어지며, AOP는 이 흐름 위에 공통 관심사를 얹는 역할을 합니다.
