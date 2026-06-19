# Attribute, Property, OOP, Interface — 객체지향 기초 정리

객체지향을 다루면서 헷갈렸던 용어들(Attribute / Property)부터, OOP 4대 특징, 인터페이스, CRUD, CQRS까지 한 번에 정리했습니다.

## 1. Attribute vs Property

형식적으로는 따옴표(`""`)의 유무처럼 사소해 보이지만, 둘의 역할은 분명히 다릅니다.

- **Attribute** = 실제 데이터를 저장하는 공간 (필드, 변수)
- **Property** = 그 데이터를 **읽거나 수정할 수 있는 방법** (getter/setter)

예를 들어 `name`, `age`는 **속성(Attribute)** 이고, `getName()`, `setName()`은 **프로퍼티(Property)** 역할입니다.

```html
<input type="text" id="email" name="email" placeholder="이메일을 입력하세요" value="a">
```
위에서 `type`, `id`, `name`, `value`, `placeholder`는 모두 **Attribute**입니다.

```java
private String userId;
```
여기서 `userId`는 **Property**입니다.

> **둘의 차이는 "값이 변경되느냐"로 볼 수 있습니다. Attribute는 기능 없이 값이 잘 바뀌지 않는 DB 같은 존재이고, Property는 기능이 있어 값이 얼마든지 바뀔 수 있는 존재입니다.**

## 2. OOP 4대 특징

### 은닉화 (Encapsulation)
`private`, `get`, `set` 등으로 외부 접근을 제한합니다. 데이터를 보호하고 유지보수성을 높입니다.

### 상속 (Inheritance)
부모의 속성과 기능을 자식이 가져옵니다.

### 추상화 (Abstraction)
인터페이스를 구현하거나 추상 클래스를 사용합니다. **목적은 불필요한 세부사항을 감추고 필요한 기능만 노출**하는 것입니다. 자동차를 운전할 때 엔진 내부 동작은 몰라도 핸들·가속페달만 알면 되는 것과 같습니다.

### 다형성 (Polymorphism)
**하나의 메서드나 객체가 여러 형태로 동작**하는 것입니다.

- **오버로딩(Overloading)**: 같은 이름, 매개변수 다름 (컴파일 시 결정)
- **오버라이딩(Overriding, `@Override`)**: 부모 메서드를 자식이 재정의 (실행 시 결정)

## 3. Interface — 부모·자식 관계

인터페이스는 기본 세팅을 부모·자식 관계로 잡습니다. **부모 하나에 자식은 여러 명**이 가능합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251023124816.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251023124825.png]]
인터페이스로 여러 클래스를 하나로 통합/연결합니다. 위가 부모, 아래가 자식 관계이며, 이런 식으로 자식을 여러 개 만들 수 있습니다. 앞 이미지가 **설계도**라면, 뒤 이미지는 그 설계도를 기준으로 구현한 **기능(메서드)** 입니다.

## 4. CRUD

| 약자 | 의미 | 설명 | HTTP 메서드 |
| --- | --- | --- | --- |
| **C** | Create | 새로운 데이터를 생성(등록) | `POST` |
| **R** | Read | 데이터를 조회(읽기) | `GET` |
| **U** | Update | 기존 데이터를 수정 | `PUT` 또는 `PATCH` |
| **D** | Delete | 데이터를 삭제 | `DELETE` |

회원가입을 예로 들면, 사용자가 내용을 입력해 보내면 `Read`로 읽고, 그 내용이 데이터에 없으면 `Create`로 만들고, 비밀번호나 아이디를 바꿀 때 `Update`, 계정을 삭제할 때 `Delete`가 됩니다. 같은 구조를 구매/판매 등 다양한 도메인에 적용할 수 있습니다.

| 어노테이션 | HTTP 메서드 | CRUD 역할 | 예시 |
| --- | --- | --- | --- |
| `@GetMapping` | GET | **조회(Read)** | 유저 목록, 게시글 보기 |
| `@PostMapping` | POST | **생성(Create)** | 새 글 등록, 회원가입 |
| `@PutMapping` | PUT | **수정(Update)** | 게시글 전체 수정, 프로필 전체 변경 |
| `@PatchMapping` | PATCH | **부분 수정(Update)** | 게시글 제목만 수정 등 일부 변경 |
| `@DeleteMapping` | DELETE | **삭제(Delete)** | 게시글, 사용자 삭제 |

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251023154646.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251023155455.png]]

## 5. CQRS — 명령과 조회의 분리

> **CQRS: 명령(Command)과 조회(Query)의 책임을 분리하는 패턴.**

| 구분 | 역할 | 예시 메서드 | 특징 |
| --- | --- | --- | --- |
| **Command** | 데이터 변경(쓰기) | Create, Update, Delete | 상태(state)를 바꿈 |
| **Query** | 데이터 조회(읽기) | Read | 상태(state)를 읽기만 함 |

`Query(Read)`만 따로 구분한 이유는, **조회 기능이 상대적으로 훨씬 많이 사용**되기 때문입니다. 그래서 따로 떼어 두고 관리합니다. 다양한 타입의 클래스들을 하나로 통합/연결하기 위해, 메서드 구조를 동일하게 맞춥니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251024131934.png]]
`productsDTO`를 `products`에 담아 HTML로 나오기까지, 값이 절대 바뀌지 않습니다.

참고로 인터페이스 형태에 따라 **CUI**(명령 기반)와 **GUI**(그래픽 기반)로 나뉩니다.
