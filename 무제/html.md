
input type="password" id="password" name="password" placeholder="비밀번호를 입력하세요"
여기서 input type="password"와 input type=password의 차이 "password"는 문자(자연어) 처리
password는 숫자(기계어)처리 이 값이 자바로 넘어갈떄 나오는 값이 달라짐 그래서 input type="password" 라고 씀 번역하지 말라action="../index.html"
 id->javascript로 보냄
 name->java로 보냄

`String`은 문자(`char`) 여러 개를 **연속으로 저장한 데이터 타입**입니다.  
예를 들어:

`String name = "홍길동";`

여기서 `"홍길동"`은 문자 3개가 연결된 **문자열(String)** 입니다



"" = string
'' = char
?email=a&password=b

?=쿼리 
email=a&password=b =스트링
email이 key   a가 value
password이 key b가 value

상위폴더 하위폴더 


@GetMapping("/hello")
public String hello() {
    return "home";
}

➡️ 브라우저에서 `http://localhost:8080/hello` 로 요청을 보내면  
이 메서드가 실행됩니다.

객체 
개체

프로퍼티(Property) 객체가 가진 데이터 (속성)

객체 = 속성+기능
개체 = 객체-기능
함수 = 객체-속성

model는 객체domain과 service에 집합
domain 속성,지식
service 기능