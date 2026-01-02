view에서 값을 contorller로 넘겨주면 domain에 DTO로 가고 service로 간다
DB에서 VO로 가고 service로 간다 service에서 DTO와 VO가 만나 view에서 준값이 맞는지 판별 후 값이 contorller을 걷치고 view로 온다

Repository 소비자가 보낸값을 DB로 보내는 과정중 속성만 남기고 기능은 지움

DTO,VO,Entity
셋다 pojo형 자바내부에만 있는 기능들만 이용한 것 @Controller등 안씀 프레임워크 의존성 없음 (Spring, Hibernate 등과 분리)



리스판스 객체 DB에서 하면 까지 가는 응답 겟터만 잇으면 됨
리퀘스트 객체 화면에서 db까지 가는 요청 겟터 셋터 둘다 있어야함


Entity
![[Pasted image 20251105101906.png]]

jpa
엔티티에 DB로 가는 마지막 관문 터널이라 생각 하면됨
엔티티는 테이블에 스키마을 그대로 가지고 있음 사실 엔티티가 가지고잇는 컬럼? 
name이나 position 이런것들이 테이블에 스키마로 만들어짐 만들어지게 해야함 
테이블을 다른방법으로 만드는 즉 스프링터미널에서 만들수도 잇지만 이렇게 테이블로

크로스 엔티티 양방향