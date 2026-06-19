# HTML — input 타입, 쿼리스트링, 그리고 객체/개체/함수

HTML 폼이 자바로 값을 넘기는 방식과, 그 과정에서 등장하는 문자열·쿼리스트링·객체 개념을 정리했습니다.

## 1. input type — 따옴표의 유무가 다르다

```html
<input type="password" id="password" name="password" placeholder="비밀번호를 입력하세요">
```

여기서 `type="password"`와 `type=password`는 처리 방식이 다릅니다.

- `"password"` — 문자(자연어)로 처리
- `password` — 숫자(기계어)로 처리

이 값이 자바로 넘어갈 때 나오는 값이 달라지므로, **번역되지 않도록 `type="password"`** 처럼 따옴표를 써 줍니다.

또한 속성별로 값이 전달되는 곳이 다릅니다.

- `id` → JavaScript로 보냄
- `name` → Java로 보냄

## 2. 문자열(String)과 char

> `String`은 문자(`char`) 여러 개를 연속으로 저장한 데이터 타입입니다.

```java
String name = "홍길동";
```
여기서 `"홍길동"`은 문자 3개가 연결된 문자열(String)입니다.

- `""` = String
- `''` = char

## 3. 쿼리스트링

```
?email=a&password=b
```

- `?` = 쿼리 시작
- `email=a&password=b` = 스트링
- `email`은 key, `a`는 value / `password`는 key, `b`는 value

## 4. 컨트롤러 매핑 예시

```java
@GetMapping("/hello")
public String hello() {
    return "home";
}
```

브라우저에서 `http://localhost:8080/hello`로 요청을 보내면 이 메서드가 실행됩니다.

## 5. 객체 · 개체 · 함수

- **객체** = 속성 + 기능
- **개체** = 객체 − 기능 (속성만)
- **함수** = 객체 − 속성 (기능만)

그리고 `model`은 `domain`과 `service`의 집합입니다.

- **domain**: 속성, 지식
- **service**: 기능
