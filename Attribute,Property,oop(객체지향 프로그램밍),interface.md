
"" 형식적으론 ""이거 있고 없고에 차이

**Attribute** = 실제 데이터 저장 공간 (필드, 변수)

**Property** = 그 데이터를 **읽거나 수정할 수 있는 방법** (getter/setter)

- `name`과 `age` → **속성(Attribute)**
    
- `getName()`, `setName()` → **프로퍼티(Property)** 역할

Attribute
input type="text" id="email" name="email" placeholder="이메일을 입력하세요" value="a"
에서 type,id,name,value,placeholder은 attribute

Property
private String userId;
에서  userld는 property

어튜리뷰티 와 프로퍼티는 값이 변경된다 안된다로 차이을 볼수있다 
어튜리뷰티는 기능이 없는 값이 봐뀌지 않게 아는 DB같은 존재
프로퍼티는 기능이 있는 값이 얼마든지 봐뀔수있는 존재


oop
4대 특징

**은닉화**
private,get,set 등 외부접근을 제한
데이터 보호, 유지보수 용이성 향상

**상속**
부모의속성을 기능도 
자식이 가져온다 

**추상화**
인터페이스을 구현 추상클래스을 사용
**목적:** 불필요한 세부사항을 감추고 **필요한 기능만 노출**
자동차를 운전할 때, 엔진 내부 동작은 몰라도 **핸들·가속페달**만 알면 됨

**다형성**
**하나의 메서드나 객체가 여러 형태로 동작**하는 것
- **오버로딩(Overloading)** – 같은 이름, 매개변수 다름 (컴파일 시 결정)
    
- **오버라이딩(Overriding)** – 부모 메서드를 자식이 재정의 (실행 시 결정)


오버로딩(Overloading) – 같은 이름, 매개변수 다름 (컴파일 시 결정)
@Override오버라이딩(Overriding) – 부모 메서드를 자식이 재정의 (실행 시 결정)



interface
부모 자식 관계로 
부모하나에 자식은 여러명가능

![[Pasted image 20251023124816.png]]


![[Pasted image 20251023124825.png]]
클래스을 하나로 통합/연결

이런 꼴로 위에가 부모
아래가 자식관계
이런식으로 자식을 여러명 만들

위사진이 설계도
밑사진이 설계도 기준으로 기능 메소드


CRUD
|약자|의미|설명|HTTP 메서드|

|**C**|**Create**|새로운 데이터를 생성(등록)|`POST`| 
|**R**|**Read**|데이터를 조회(읽기)|`GET`|
|**U**|**Update**|기존 데이터를 수정|`PUT` 또는 `PATCH`| 
|**D**|**Delete**|데이터를 삭제|`DELETE`|

하나의 어플리케이션을 만든다고 하면 
각각에 역할 분배 회원가입으로 예을 들면 
사용자가 내용을 입력하고 보내면 read읽고 
사용자가 보낸 내용이 데이터에 없으면 create만듬
update은 사용자가 비번이나 아이디을 바꿀때 
delet는 계정을 삭제할때 이런 예시로 구매판매에도 적용 시키고 다양하게 적용시킬수있음 


| 어노테이션            | HTTP 메서드 | CRUD 역할           | 예시                   |
| ---------------- | -------- | ----------------- | -------------------- |
| `@GetMapping`    | GET      | **조회(Read)**      | DB에서 유저 목록, 게시글 보기   |
| `@PostMapping`   | POST     | **생성(Create)**    | 새 글 등록, 회원가입         |
| `@PutMapping`    | PUT      | **수정(Update)**    | 게시글 전체 수정, 프로필 전체 변경 |
| `@PatchMapping`  | PATCH    | **부분 수정(Update)** | 게시글 제목만 수정 등 일부 변경   |
| `@DeleteMapping` | DELETE   | **삭제(Delete)**    | 게시글, 사용자 삭제          |


![[Pasted image 20251023154646.png]]

![[Pasted image 20251023155455.png]]

CQRS
즉, **명령(Command)** 과 **조회(Query)** 의 **책임을 분리하는 패턴**
|구분|역할|예시 메서드|특징|


|**Command**|데이터 변경 (쓰기)|Create, Update, Delete|상태(state)를 바꿈|
|**Query**|데이터 조회 (읽기)|Read|상태(state)를 읽기만 함|

Query(read)만 따로 구분한 이유 상대적으로 조회하는 기능이 ㅈㄴ 많이 사용됨
그래서 따로 구분해놓고 

다양한 타입에 클래스들을 하나도 (통합/연결하기위해서)
메소드에 구조을 똑같이

![[Pasted image 20251024131934.png]]
productsDTO을 products에 담아서 html에서 나오기 까지 값이 절대 봐뀌지않는다
