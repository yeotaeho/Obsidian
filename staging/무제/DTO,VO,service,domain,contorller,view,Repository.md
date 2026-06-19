# DTO · VO · Service · Domain · Controller · View · Repository

스프링 MVC 레이어에서 값이 오가는 흐름과, DTO·VO·Entity의 차이를 정리했습니다.

## 1. 값이 오가는 흐름

View에서 값을 Controller로 넘기면, 그 값은 Domain에서 **DTO**로 만들어져 Service로 갑니다. 한편 DB에서 온 값은 **VO**로 만들어져 Service로 옵니다. Service에서 DTO와 VO가 만나 View에서 준 값이 맞는지 판별한 뒤, 그 값이 Controller를 거쳐 다시 View로 돌아옵니다.

```
View → Controller → (Domain) DTO → Service
DB   → VO → Service
Service(판별) → Controller → View
```

**Repository**는 소비자가 보낸 값을 DB로 보내는 과정에서, **속성만 남기고 기능은 지우는** 역할을 합니다.

## 2. DTO · VO · Entity의 공통점

셋 다 **POJO**형으로, 자바 내부 기능만 이용합니다(`@Controller` 등을 쓰지 않음). 즉 **프레임워크(Spring, Hibernate 등)에 대한 의존성이 없습니다.**

## 3. Response 객체 vs Request 객체

- **Response 객체**: DB에서 화면까지 가는 **응답**. getter만 있으면 됩니다.
- **Request 객체**: 화면에서 DB까지 가는 **요청**. getter와 setter 둘 다 있어야 합니다.

## 4. Entity와 JPA

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251105101906.png]]

> **엔티티는 DB로 가는 마지막 관문(터널)이라고 생각하면 됩니다.**

엔티티는 테이블의 스키마를 그대로 가지고 있습니다. 사실 엔티티가 가진 컬럼(`name`, `position` 등)이 곧 테이블의 스키마로 만들어집니다. 테이블을 스프링 터미널에서 직접 만들 수도 있지만, 이렇게 엔티티를 통해 테이블을 만드는 방식을 사용합니다.

또한 엔티티는 **크로스 엔티티로 양방향** 연결이 가능합니다.
