선형구조- 리스트 순서대로 array구조 (ex [1,2,3,4,5]

비선형구조-키값으로 호출 json구조 (ex {name:yeotaeho,age:22,}

데이터 베이스는 비선형구조

ai line,non-line
자바 array + list , map
파이썬 list , dictionary
자바스크립트 array , json

아키텍처(Architecture) 사용자(브라우저)
					   ↓
					Controller (요청 받음)
					   ↓
					Service (비즈니스 로직 처리)
					   ↓
					Repository (DB 접근)
					   ↓
					Database (데이터 저장)



워크로드 

엘리먼트 [1,2,3]
이중에 1이 엘리먼트 2도 될수있오 3 도 될수있음 즉 데이터베이스 안에 있는 
데이터 값을 엘리먼트

<div class="card">
    <h1>타이틀</h1>   <!-- h1 태그도 하나의 엘리먼트 -->
    <p>설명 문구</p>   <!-- p 태그도 엘리먼트 -->
</div>
<div>: 부모 엘리먼트

<h1>과 <p>: 자식 엘리먼트

속성(class="card")도 엘리먼트의 일부로 간주


디스크 데이터베이스는 디스크에 저장됨 안에 있는 언어 다 자연어

메모리

oop
객체지향 기술

타임리프 - 컨트롤러 - 서비스 - 레포지토리(서비스에서 넘어온 기능역활을 지우고 DB한테 전달) - DB
도메인 _Entity_ 레포지토리에서 이용하는 도메인
가장 기본적인 파이프라인

List<Map<String, String>>
<> = 스트럭처

스트럭쳐

프로그래밍 할때 자료 구조(Data Structure) 데이터를 저장/관리하는 방식 List, Map, Struct
소프트웨어 구조(Software Structure)코드 계층의 설계 MVC, Layered Architecture
시스템 구조(System Structure)서버/DB 등 전체 연결 구조 클라이언트–서버 구조