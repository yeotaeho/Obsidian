view에서 값을 contorller로 넘겨주면 domain에 DTO로 가고 service로 간다
DB에서 VO로 가고 service로 간다 service에서 DTO와 VO가 만나 view에서 준값이 맞는지 판별 후 값이 contorller을 걷치고 view로 온다

Repository 소비자가 보낸값을 DB로 보내는 과정중 속성만 남기고 기능은 지움

DTO,VO,Entity
셋다 pojo형 자바내부에만 있는 기능들만 이용한 것 @Controller등 안씀 프레임워크 의존성 없음 (Spring, Hibernate 등과 분리)